# Axios >= 1.3.2 < 1.7.4
 - CVE: [CVE-2024-39338](https://www.cve.org/CVERecord?id=CVE-2024-39338)
 - Snyk ID: [SNYK-JS-AXIOS-7361793](https://security.snyk.io/vuln/SNYK-JS-AXIOS-7361793)
 - CVSS: [8.8](https://security.snyk.io/vuln/SNYK-JS-AXIOS-7361793#cvss)

## Overview
Axios is a popular promise-based HTTP client for the browser and Node.js. It is used to make HTTP requests from the client-side to the server-side. The library is widely used in web applications to make AJAX requests.

Axios versions >= 1.3.2 < 1.7.4 are vulnerable to Server-Side Request Forgery (SSRF) attacks. This vulnerability allows an attacker to make requests to internal resources, bypassing the same-origin policy. This can lead to unauthorized access to internal systems, data exfiltration, and other security risks.

### Protocol-Relative URLs
A protocol-relative URL is a URL that does not specify a protocol (http or https). For example, `//example.com` is a protocol-relative URL. When Axios is used to make a request to a protocol-relative URL, it will use the protocol of the current page (http or https). This can lead to SSRF vulnerabilities if the URL is controlled by an attacker.

Axios introduced in version 1.3.2 the ability to make requests to protocol-relative URLs. However, the implementation of this feature was vulnerable to SSRF attacks. An attacker could craft a protocol-relative URL pointing to an internal resource, and Axios would make the request to that resource.

```javascript
 * @returns {boolean} True if the specified URL is absolute, otherwise false
 */
export default function isAbsoluteURL(url) {
  - // A URL is considered absolute if it begins with "<scheme>://" or "//" (protocol-relative URL).
  + // A URL is considered absolute if it begins with "<scheme>://".
  // RFC 3986 defines scheme name as a sequence of characters beginning with a letter and followed
  // by any combination of letters, digits, plus, period, or hyphen.
  - return /^([a-z][a-z\d+\-.]*:)?\/\//i.test(url);
  + return /^([a-z][a-z\d+\-.]*:)\/\//i.test(url);
}
```
#### Let’s break it down:

1. `^`: This asserts the start of the string. It means the regular expression will try to match from the beginning of the input string.

2. `([a-z][a-z\d+\-.]*:)?`:

- `[a-z]`: This expects a lowercase letter as the first character of the protocol (e.g., h in http, f in ftp).
- `[a-z\d+\-.]*`: This allows for a combination of lowercase letters (a-z), digits (\d), plus signs (+), periods (.), and hyphens (-) after the first letter. This is matching the rest of the protocol name, such as http, ftp, or mailto.
- `:`: The protocol must be followed by a colon (:).
- `?`: **This makes the whole group optional**, meaning it can match with or without the protocol. So, both http://example.com and //example.com would be valid matches. **THIS IS THE VULNERABILITY**.
3. `\/\/`: This checks for // after the optional protocol. These two slashes are required regardless of whether a protocol is present.

4. `i`: This is a flag that makes the match case-insensitive. For instance, it will match HTTP://, http://, or any case variation.

5. `.test(url)`: This method tests if the given URL (url) string matches the regular expression. It returns true if the string matches, and false otherwise.

#### In summary:
This regular expression is designed to check if the input URL string starts with either:

- A protocol followed by `://` (like `http://` or `ftp://`), or
- Just `//` (for protocol-relative URLs).

For example, it would match:

- `http://example.com`
- `https://example.com`
- `ftp://example.com`
- `//example.com`

And it would return `true` for valid cases and `false` for invalid ones.

#### The Fix
The key fix here was removing the `?` from the regular expression, which was allowing protocol-relative URLs to be considered absolute. This change ensures that only URLs that begin with `<scheme>://` are considered absolute, preventing SSRF attacks via protocol-relative URLs.

## Remediation
Upgrade to Axios version 1.7.4 or later to fix this vulnerability. The maintainers have released a patch to address this issue. You can find more information in the [GitHub PR](https://github.com/axios/axios/commit/07a661a2a6b9092c4aa640dcc7f724ec5e65bdda)