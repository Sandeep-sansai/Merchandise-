Here’s how you can structure the Spring Boot project with an in-memory LRU cache using ConcurrentHashMap and include API endpoints, preloading from the database, and scheduled refresh every hour.

1. Project Structure

	•	CacheConfig: To define the LRU cache and configuration.
	•	Controller: To expose API endpoints for cache operations.
	•	DAO: To handle database operations.
	•	Entity: To represent the database entity.
	•	Pojo: To hold POJOs.
	•	Service: To handle the business logic.
	•	Main Package: To contain the main Spring Boot application.

2. Implementation

CacheConfig

package com.example.cache.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.LinkedHashMap;
import java.util.Map;

@Configuration
public class LRUCacheConfig {

    private static final int CACHE_SIZE = 100;

    @Bean
    public Map<String, Object> lruCache() {
        return new LinkedHashMap<String, Object>(CACHE_SIZE, 0.75f, true) {
            @Override
            protected boolean removeEldestEntry(Map.Entry<String, Object> eldest) {
                return size() > CACHE_SIZE;
            }
        };
    }
}

Entity

package com.example.cache.entity;

import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class CacheEntity {

    @Id
    private String key;
    private String value;

    // Getters and setters
}

DAO

package com.example.cache.dao;

import com.example.cache.entity.CacheEntity;
import org.springframework.data.jpa.repository.JpaRepository;

public interface CacheRepository extends JpaRepository<CacheEntity, String> {
}

POJO

package com.example.cache.pojo;

public class CacheEntry {
    private String key;
    private String value;

    // Getters and setters
}

Service

package com.example.cache.service;

import com.example.cache.dao.CacheRepository;
import com.example.cache.entity.CacheEntity;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import javax.annotation.PostConstruct;
import java.util.Map;

@Service
public class CacheService {

    @Autowired
    private CacheRepository cacheRepository;

    @Autowired
    private Map<String, Object> lruCache;

    @PostConstruct
    public void loadCacheAtStartup() {
        cacheRepository.findAll().forEach(cacheEntity -> 
            lruCache.put(cacheEntity.getKey(), cacheEntity.getValue())
        );
    }

    public String getFromCache(String key) {
        return (String) lruCache.get(key);
    }

    public void addToCache(String key, String value) {
        lruCache.put(key, value);
        cacheRepository.save(new CacheEntity(key, value));
    }

    public void refreshCache() {
        lruCache.clear();
        loadCacheAtStartup();
    }
}

Controller

package com.example.cache.controller;

import com.example.cache.service.CacheService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/cache")
public class CacheController {

    @Autowired
    private CacheService cacheService;

    @GetMapping("/{key}")
    public String getFromCache(@PathVariable String key) {
        return cacheService.getFromCache(key);
    }

    @PostMapping
    public void addToCache(@RequestParam String key, @RequestParam String value) {
        cacheService.addToCache(key, value);
    }

    @PutMapping("/refresh")
    public void refreshCache() {
        cacheService.refreshCache();
    }
}

Scheduler for Cache Refresh

package com.example.cache.config;

import com.example.cache.service.CacheService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class CacheRefreshScheduler {

    @Autowired
    private CacheService cacheService;

    @Scheduled(fixedRate = 3600000) // Refresh every hour
    public void refreshCacheScheduled() {
        cacheService.refreshCache();
    }
}

Main Application

package com.example.cache;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;

@SpringBootApplication
@EnableScheduling
public class CacheApplication {

    public static void main(String[] args) {
        SpringApplication.run(CacheApplication.class, args);
    }
}

3. Explanation

	•	CacheConfig: Defines an LRU cache using LinkedHashMap with a capacity of 100.
	•	Entity: Represents the CacheEntity in the database, with a key and value.
	•	DAO: Provides JPA repository to interact with the database.
	•	Service: Contains business logic, like loading cache on startup, fetching, adding, and refreshing cache.
	•	Controller: Exposes REST API endpoints for interacting with the cache (get, add, and refresh).
	•	Scheduler: A scheduled task that refreshes the cache every hour.
	•	Main Application: Enables scheduling and starts the Spring Boot application.

This structure ensures that the cache is loaded from the database before the ApplicationContext is fully initialized and is refreshed regularly via a scheduler. You can also trigger a manual refresh via the exposed API endpoint.