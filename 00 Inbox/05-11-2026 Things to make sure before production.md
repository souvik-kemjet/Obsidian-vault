
### Before production

Grouping the auth endpoints by IP is correct, but two things need fixing before go-live, or production will have the same problem:

- **Proxy setup:** behind a load balancer or reverse proxy, `ForwardedHeadersOptions` needs `KnownProxies` / `KnownNetworks` configured. Otherwise every user looks like the load balancer’s IP.
- **Shared mobile IPs:** Indian mobile carriers put many users behind one public IP. 10 OTP requests per 15 minutes per IP will block real tourists. Use a much higher per-IP limit and rely on the per-phone database limits for abuse protection. Don’t group limits by a device ID the client sends, because an attacker can just change it on every request.