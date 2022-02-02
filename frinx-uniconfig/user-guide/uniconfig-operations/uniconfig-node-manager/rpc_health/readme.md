# RPC health

This RPC checks if UniConfig is running. If database persistence is
enabled it checks database connection too.

## RPC Examples

RPC health input is empty and RPC output contains result of operation.

```bash RPC Request
curl --location --request POST 'http://localhost:8181/rests/operations/uniconfig-manager:health' \
--header 'Accept: application/json'
```

**Response when database persistence is disabled:**

```json RPC Response, Status: 200
{
    "output": {
        "healthy": true,
        "message": "DB persistence is disabled"
    }
}
```

**Response when database persistence is enabled and database connection is valid:**

```json RPC Response, Status: 200
{
    "output": {
        "healthy": true,
        "message": "DB connection is alive"
    }
}
```

**Response when database persistence is enabled and database connection is not valid:**


```json RPC Response, Status: 200
{
    "output": {
        "healthy": false,
        "message": "Error connecting to DB"
    }
}
```
