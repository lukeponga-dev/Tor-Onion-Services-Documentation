# Summary of Best Practices for Hosting Onion Services
The Onion Router (Tor) enables the creation and hosting of Onion Services (formerly known as "Tor Hidden Services"), which allow servers and clients to connect anonymously. This guide provides best practices for installing, configuring, and securing Onion Services.
> How to Use This Guide
> This guide offers insights based on experiences running Onion Services and includes helpful tips. Contributions and translations are welcomed to enhance its usefulness.
---
Installing and Configuring Onion Services
---
```markdown
1. Keep Tor Software Updated

  Importance: Regularly update your Tor software to patch critical security flaws.
  Action: Ensure you're running the latest version of Tor to maintain security and stability.

2. Explore Diverse Uses of Onion Services

  Versatility: Beyond websites, you can host services like IMAP, SMTP, and facilitate mail transfer between MTAs over Onion Services.
  Caution: Be wary of services making DNS requests, as they can leak information.
  Mitigation: Configure your server to route all traffic through Tor using firewall rules (e.g., iptables).

3.Avoid Running a Relay and an Onion Service on the Same Instance

  Reason: Running both can aid traffic correlation and fingerprinting attacks.
  Best Practice: Use separate machines or instances for relays and Onion Services.

4. Monitor Your Onion Service's Availability

  Why: Onion Services can fail for various reasons despite improved stability.
  Solution: Set up regular monitoring to ensure your service is functioning properly.

5. Configure Multiple Ports for a Single Onion Service

  Flexibility: Use multiple HiddenServicePort lines in your Tor configuration
  to expose multiple services on different ports.

# torrc
    HiddenServiceDir /usr/local/etc/tor/other_hidden_service/
    HiddenServicePort 6667 127.0.0.1:6667
    HiddenServicePort 22 127.0.0.1:22

```
> Note: To run multiple Onion Services from the same Tor client, add additional HiddenServiceDir entries in the configuration.
> 
```MARKDOWN
6. SSL/TLS Considerations
  Not Required: SSL/TLS isn't necessary because Tor provides end-to-end encryption with Perfect Forward Secrecy (PFS).
  Additional Layers: Using SSL/TLS doesn't hurt but be aware of potential certificate validation issues with .onion domains.
  Solution: Obtain a certificate for your .onion address if possible.
  Caution: Ensure your Onion Service doesn't send certificates intended for external websites.

7. Configuring Onion Services with Rails 4
  Disable Force SSL in Production:
  # Change config.force_ssl = true to config.force_ssl = false in config/environments/production.rb.
  Re-Enable Secure Cookies and HSTS for HTTPS:
  Install the secure_headers gem in your Gemfile:
  # ruby
    gem 'secure_headers', '~> 3.5'

  Configure secure_headers in config/initializers/secureheaders.rb:
# ruby
    ExplainSecureHeaders::Configuration.default do |config|
    config.cookies = {
      secure: true,
      httponly: true,
      samesite: {
        strict: true
      }
    }
  end

Web Server Configuration:
  For the Onion Service (port 80), do not set X_FORWARDED_PROTO to https.
  For the non-Onion Service (port 443), set X_FORWARDED_PROTO to https.
  Preventing Your Onion Service from Being Discovered

1. Protect Against Information Leakage

  Server Signatures: Disable or obfuscate server banners that reveal software versions and operating system details.
  Referrer Headers: Users navigating between Onion Services and clearnet sites may leak the Onion address.
  Mitigation: Encourage users to use the latest Tor Browser, which minimizes such leaks.

2. Separate Clearnet and Onion Services

  Host Headers: Ensure your server doesn't respond to requests intended for the other service based on the Host header.
  Network Binding: Bind your Onion Service to an internal IP and the clearnet service to the external IP.
  Isolation: Run your Onion Service on a machine without an external IP address to prevent accidental exposure.

3. Maintain Accurate Server Time

  Reason: Time discrepancies can help identify your server.
  Action: Use Network Time Protocol (NTP) to correct and synchronize your server's clock automatically.
4. Avoid Exposing Information via Applications
  PHP and Server Variables: Be cautious with scripts that may expose phpinfo(), $_SERVER, or detailed error messages.
  Error Handling: Configure applications to display generic error messages.
5. Guard Against Server-Side Request Forgery (SSRF)
  Risk: SSRF attacks trick your server into making external requests, potentially revealing its location.
  Solution: Implement strict egress firewall rules to block unauthorized outbound connections.
6. Acknowledge Long-Term Risks
  Exposure Over Time: The longer your Onion Service is online, the higher the risk of its location being discovered through traffic analysis and availability patterns.
  Stay Informed: Follow discussions and updates from the Tor Project on mitigating these risks.
  Additional Recommendations
  OnionScan: Use tools like OnionScan to detect leaks of IP addresses, metadata, and misconfigurations.
  Promote Onion Services: Encourage users to discover your Onion Service through authenticated postings, DNS records, or digital signatures.
  Advocate for Adoption: Ask other service providers to offer Onion Services to enhance privacy and security.
  Protecting Your Onion Service
1. Safeguard Private Keys
  Access Control: Ensure your private keys are secure and only accessible by the Tor process.
  Permissions: Set appropriate file permissions to prevent unauthorized access or reading.
2. Backup Private Keys
  Regular Backups: Keep encrypted backups of your private keys to prevent loss of your Onion address in case of system failure.
  Secure Storage: Store backups in a safe, offline location.
3. Avoid Localhost Bypasses
Risk: Services intended for localhost (e.g., mod_status) may become accessible through the Onion Service.
  Mitigation Strategies:
  Run the Onion Service on a different machine or virtual environment.
  Configure services to bind to specific interfaces or use Unix sockets.
  Regularly audit configurations to ensure localhost services aren't exposed.
4. Require Authentication for Your Onion Service
  Access Control: Use HiddenServiceAuthorizeClient in your Tor configuration to restrict access to authorized clients.
  Example:
# torrc
      HiddenServiceDir /var/lib/tor/hidden_service/
      HiddenServicePort 80 127.0.0.1:80
      HiddenServiceAuthorizeClient stealth client_name

Types of Authentication:
Basic: Requires a key to connect.
Stealth: Hides the service and requires authentication, enhancing security.
5. Protect Against Advanced Attacks
Vanguards Add-on: Install the Vanguards tool to defend against sophisticated attacks, such as guard discovery and traffic analysis.
Sandboxing: Enable Tor's sandboxing feature by adding Sandbox 1 to your torrc file.
