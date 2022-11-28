# Website well-known Contracts [Draft]

<aside>
🚧 This document is a work in progress, we are gathering feedback and suggestions to arrive at a complete specification.

</aside>

Inspired by Content Security Policy (CSP) [https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

# Problem Definition

Currently there is no standard way to tell what contracts a website is expected to call. This means that a hacked site can request a signature for any contract, it can steal your tokens, cause you to sign a multisig compromising an organization, or otherwise damage the user.

Another problem is that any site can call a smart contract, and this means that any site is part of the attack surface. A hack on aj ecommerce store, a blog, or really any web page can lead to a legitimate site draining a user's wallet.

# Proposed Solution

Websites that interact with smart contracts should publish a list that let's wallets and other interfaces know what contracts the website is expected to call.

Any transaction from a site that requests a contract not on the list can then be treated as suspicious, and the user can be warned.

Also, as standards become more widely adopted, sites should be treated as unexpected to call a smart contract unless they explicitly declare it by providing a list.

# Technical Details

## Format

```jsx
//.well-known/contracts
{
  contracts: [
    {
      chainId: "string",
      contracted: "string",
    },
  ];
}
```

## Cases

### No well-known is present

The site is not expected to call any smart contacts. If it does call a smart contract treat it as suspicious. This is how CORS works where by default all External requests are blocked.

```jsx
//.well-known/contracts
undefined;
```

### wild card

Any smart contract is allowed from any chain

```jsx
//.well-known/contracts
{
  contracts: [
    {
      chainId: "*",
      contracted: "*",
    },
  ];
}
```

### Wild Card for Networks/Chains

In this sample only networks on Ethereum (1) and Polygon (137) are allowed.

```jsx
//.well-known/contracts
{
	contracts:
	[
		{
			"chainId": "1",
			"contracted": "*"
		}
		{
			"chainId": "137",
			"contracted": "*"
		}
	]
}
```

You could also set up TestNet IDs instead in your test environment.

### Specific list

Check if a contract and chain are on the list. Any call not on the list should be blocked or at least warned about.

# Wallet implementation

We recommend warning users. Outright blocking similar to CSP can be done once the standard is more widely accepted.

## Early Stage

The contract list is fully checked

Sites without a contract list are not warned about since the standard is still being adopted.

## End Game

Once the standard has been adopted allowing website developers with contracts to have time to at least add a wildcard on their site.

Any site that asks to sign a transaction for a contract without a contract list present should trigger a warning to the user.

Contract list is fully checked

# Open Questions

## .well-known/contracts page vs Using Headers vs <meta> Tag

Is the best way to share and expose this data through a page server on the domain, something like [mysite.com/.well-known/contracts](http://mysite.com/.well-known/contracts) , or by using headers on the site itself similar to CSP.

Another alternative is using the meta tag, similar to CSP [https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

```html
<meta
  http-equiv="Content-Security-Policy"
  content="default-src 'self'; img-src https://*; child-src 'none';"
/>
```

# References

Content Security Policy (CSP) [https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

Yearn Finance Allowlist [https://medium.com/iearn/yearn-allowlist-71757d4e3cf4](https://medium.com/iearn/yearn-allowlist-71757d4e3cf4)

Uniswap Token Lists [https://uniswap.org/blog/token-lists](https://uniswap.org/blog/token-lists)
