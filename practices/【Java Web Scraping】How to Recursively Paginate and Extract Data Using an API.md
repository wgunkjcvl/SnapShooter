
# 【Java Web Scraping】How to Recursively Paginate and Extract Data Using an API

Efficiently scraping web data through APIs can be challenging, especially when dealing with paginated data. In this guide, we’ll walk through a practical example of recursively retrieving paginated API data, handling rate limits, and persisting the results into a database. Whether you’re scraping IP data or other information, these steps will help streamline the process.

---

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Introduction

In a recent development project, we needed to collect user IP information for analytics. These IPs were accessed through third-party services, segmented into groups, and stored as paginated data (10 records per page). Since querying large amounts of data at once might cause the API to fail, we had to design a solution to recursively request and process the data page by page.

### Problem Breakdown

Here’s a high-level approach to solving this problem:

1. **Retrieve API endpoint URL**: Identify the API endpoint and required parameters (headers, query parameters, etc.).
2. **Design pagination logic**: Use recursion and looping to handle paginated responses.
3. **Rate limiting**: Ensure requests are sent at a controlled frequency to avoid being blocked.
4. **Data processing**: Parse and persist the extracted data into a database.

---

## Step 1: Simulating HTTP Requests

Most projects require consuming external APIs to fetch data. This section explains how to identify an API endpoint from a web page and simulate requests. 

Using Google Chrome as an example:

1. Open **Developer Tools** → Go to the **Network** tab.
2. Filter by **Fetch/XHR** requests.
3. Right-click on the desired URL → Select **Copy** → **Copy as cURL (bash)**.
4. Paste the cURL command into Postman to simulate the HTTP request.

Make sure to include important headers like `Cookie`, `Authorization`, and `User-Agent`. These headers ensure that the API request mimics a real browser request.

### Example Screenshot Walkthrough
- Using Chrome Developer Tools to locate the API request:
  ![Example 1](https://img2023.cnblogs.com/blog/2458865/202305/2458865-20230505162055457-122312617.png)
- Copying the request as cURL:
  ![Example 2](https://img2023.cnblogs.com/blog/2458865/202305/2458865-20230505162122986-770510459.png)

---

## Step 2: Recursive Pagination Logic

When dealing with paginated data, the response often contains metadata indicating the total pages or whether additional data is available. The following code demonstrates how to recursively fetch paginated API data in Java.

### Code Example: Recursive Pagination

```java
public boolean getIpPoolOriginLinksInfo(
        HashMap<String, Object> commonPageMap, 
        HashMap<String, String> headersMap, 
        String charset) {
    
    // API endpoint
    String linkUrl = "https://example.com/api/v1/data";
    
    // Prepare query parameters
    HashMap<String, Object> linkParamsMap = new HashMap<>();
    linkParamsMap.put("page", commonPageMap.get("page"));
    linkParamsMap.put("size", commonPageMap.get("size"));
    
    // Make HTTP request
    String httpLinkResponse = IpPoolHttpUtils.doGet(linkUrl, linkParamsMap, headersMap, charset);
    
    // Parse JSON response
    JSONObject linkJson = JSON.parseObject(httpLinkResponse).getJSONObject("data");
    JSONArray linkArray = linkJson.getJSONArray("resultList");
    
    if (linkArray != null) {
        for (Object linkObj : linkArray) {
            JSONObject info = JSON.parseObject(linkObj.toString());
            String urlId = info.getString("urlId");
            
            // Recursive call to handle nested data
            boolean flag = getIpPoolOriginRecords(urlId, commonPageMap, headersMap, charset);
            if (!flag) {
                break;
            }
        }
        
        // Handle next page
        int currentPage = Integer.parseInt(linkParamsMap.get("page").toString());
        int totalPages = (int) Math.ceil(linkJson.getInteger("total") / 10.0);
        if (currentPage < totalPages) {
            linkParamsMap.put("page", String.valueOf(currentPage + 1));
            getIpPoolOriginLinksInfo(linkParamsMap, headersMap, charset);
        }
    }
    
    return true;
}
```

---

## Step 3: Handling API Rate Limits

To avoid exceeding the API rate limits, you must control the frequency of your requests. This can be done by implementing a delay mechanism between requests.

### Code Example: Rate Limiting

```java
private void check(CommonAPICheckData commonAPICheckData) {
    int minuteCount = commonAPICheckData.getMinuteCount();
    try {
        if (minuteCount < 2) {
            Thread.sleep(2000); // Sleep for 2 seconds
        } else {
            commonAPICheckData.setMinuteCount(minuteCount - 1);
        }
    } catch (InterruptedException e) {
        throw new RuntimeException("API rate limit exceeded!");
    }
}
```

### Supporting Class

```java
@Data
public class CommonAPICheckData {
    private int secondCount = 3;   // Maximum calls per second
    private int minuteCount = 100; // Maximum calls per minute
}
```

---

## Step 4: Data Persistence

Once the data is retrieved, it should be saved to the database. The following code snippet demonstrates how to persist the data efficiently, with mechanisms to handle duplicate records.

### Code Example: Data Persistence

```java
private boolean getIpPoolDetails(CommonIpPool commonIpPool) {
    try {
        commonIpPoolService.insert(commonIpPool); // Insert into database
    } catch (DuplicateKeyException e) {
        return true; // Ignore duplicates
    } catch (Exception e) {
        throw new RuntimeException(e.getMessage());
    }
    return true;
}
```

---

## Step 5: Summary

Web scraping often involves overcoming challenges like pagination, rate limiting, and data transformation. The example shared here demonstrates recursive pagination and rate-limited API calls in Java. 

When scraping protected pages, additional steps like handling headers, cookies, or captchas may be required. These advanced techniques will be covered in future articles.

For seamless scraping without worrying about proxies or CAPTCHAs, consider using ScraperAPI. It simplifies web scraping with its robust infrastructure.

👉 [Start your free trial today!](https://bit.ly/Scraperapi)
