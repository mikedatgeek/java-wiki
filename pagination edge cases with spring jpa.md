# 📄 Handling Pagination Edge Cases in Spring Data JPA

## 🧩 Problem
In a Spring Boot application using JPA with `Pageable` and `Specification`, the frontend requests a fixed number of records per page (e.g. 100). On the last page, if fewer records exist (e.g. only 27), the backend returns an empty result, and the frontend displays nothing.

## 🎯 Goal
Ensure the backend returns a valid page with available data, even if the frontend requests more records than exist on the last page.

## ✅ Solution
Use logic to detect when the requested page number exceeds the total number of pages, and return the closest valid page instead.

### 🔧 Code Snippet
```java
int totalPages = page.getTotalPages();
int requestedPage = pageable.getPageNumber();
int pageSize = pageable.getPageSize();
Sort sort = pageable.getSort();

// validPageIndex: ensures the page index is within the range of available pages
int validPageIndex = Math.min(requestedPage, Math.max(0, totalPages - 1));

Pageable adjustedPageable = PageRequest.of(validPageIndex, pageSize, sort);
Page<MyEntity> adjustedPage = repository.findAll(specification, adjustedPageable);
