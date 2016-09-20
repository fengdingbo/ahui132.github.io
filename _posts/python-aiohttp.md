# proxy
Proxy support

    async with aiohttp.ClientSession() as session:
        async with session.get("http://python.org", proxy="http://some.proxy.com") as resp:
            print(resp.status)

it also supports proxy authorization:

    async with aiohttp.ClientSession() as session:
        proxy_auth = aiohttp.BasicAuth('user', 'pass')
        async with session.get("http://python.org", proxy="http://some.proxy.com", proxy_auth=proxy_auth) as resp:
            print(resp.status)

Authentication credentials can be passed in proxy URL:

    session.get("http://python.org", proxy="http://user:pass@some.proxy.com"

# Cookie

## Custom Cookies
To send your own cookies to the server, you can use the cookies parameter of ClientSession constructor:

    url = 'http://httpbin.org/cookies'
    cookies = {'cookies_are': 'working'}
    async with ClientSession(cookies=cookies) as session:
        async with session.get(url) as resp:
            assert await resp.json() == { "cookies": {"cookies_are": "working"}}
