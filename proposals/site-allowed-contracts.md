# Website well-known Contracts [Draft]

### Draft

<aside>
🚧 This document is a work in progress, we are gathering feedback and suggestions to arrive at a complete specification.

</aside>

### Abstract

This proposal defines a new ".well-known" web resource (as defined in [RFC-8614](https://www.rfc-editor.org/rfc/rfc8615) for EVM smart contracts. It provides an interface for website authors to hint to crypto wallets what transactions (if any) to expect from a site during a user agent's web browsing session. In doing so, it helps crypto wallets better warn users about unexpected or potentially malicious transactions.

### Inspiration

Inspired by Content Security Policy (CSP) [https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

# Problem Definition

Currently a site that requests signatures can ask you to sign any transaction, even transactions that the site was not intended for.

For example, an NFT marketplace can request to sign a multi sig that drains your DAO Treasury, or a Defi app meant only to swap tokens can request to move all your NFTs out of your wallet.

## Code Injection

Malicious code injected into a website can access all the contents and permissions of a user's wallet, where the only thing protecting a user is their own decision making.

## Rogue Developers

One rogue developer at any dapp has the potential to drain all of the dapp users' wallets.

Code reviews can help with this, but there need to be clearer mechanisms to detect and prevent these attacks.

# Proposed Solution

Websites that interact with smart contracts should publish a list at `/.well-known/contracts` that lets wallets and other interfaces know what contracts the website is expected to call.

Any transaction initiated on a site using a contract not on the list is treated as suspicious, and the user can be shown a warning

# Technical Details

## Outline

```jsx
//.well-known/contracts
{
  contracts: [
    {
      chainId: "string",
      contractId: "string",
    },
  ];
}
```

## Schema

```jsx
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "type": "object",
  "properties": {
    "contracts": {
      "type": "array",
      "items": [
        {
          "type": "object",
          "properties": {
            "chainId": {
              "type": "string"
            },
            "contractId": {
              "type": "string"
            }
          },
          "required": [
            "chainId",
            "contractId"
          ]
        }
      ]
    }
  },
  "required": [
    "contracts"
  ]
}
```

# Examples

## Specific list

Check if a contract and chain are on the list. Any call not on the list should be blocked or at least warned about.

```json
//.well-known/contracts
{
  "contracts": [
    {
      "chainId": "1",
      "contractId": "0xBd3531dA5CF5857e7CfAA92426877b022e612cf8",
    },
  ];
}
```

## Wildcard Specifier for Networks/Chains

In this sample only contracts on the Ethereum (1) and Polygon (137) networks are allowed. (Network IDs pulled from [ChainList](https://chainlist.org/))

```json
//.well-known/contracts
{
  "contracts": [
    {
      "chainId": "1",
      "contractId": "*",
    },
    {
      "chainId": "137",
      "contractId": "*",
    },
  ];
}
```

You could also set up TestNet IDs instead in your test environment.

## Wild Card

Any smart contract is allowed from any chain

```json
//.well-known/contracts
{
  "contracts": [
    {
      "chainId": "*",
      "contractId": "*",
    },
  ];
}
```

## No Contract Calls Allowed

This is a way for a site to specify it should not be interacting with any smart contracts, to protect their users. This is useful for traditional site like amazon, youtube, or your blog to specify that any transaction requested from them is an attack and should be blocked.

```json
//.well-known/contracts
{
  "contracts": []
}
```

## No well-known is present

Don't do any checks. Similar to wildcard.

# Wallet implementation

## Warn the User

Show a warning to the user when triggering a transaction if the contract does not match the list published by the website.

This should be done for all transactions coming from the site that have a contract included.

## Log an Error

A console error should be logged to make it easier for the site's developers to detect when their site is having a problem due to transactions not matching their contract list.

## Sample Implementation

A sample library of making these checks will soon be released.

# Open Questions

## .well-known/contracts page vs Using Headers vs <meta> Tag

Is the best way to expose this data via a [well-known URL](https://www.rfc-editor.org/rfc/rfc8615), ex. [mysite.com/.well-known/contracts](http://mysite.com/.well-known/contracts), or by using HTTP headers on the web server that serves the site similar to [CSP](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP).

# FAQ and Decisions

## Contract Details, Functions, and CallData

We chose to limit the scope of the spec to just the `networkId` and `contractId` to make it easier to agree on the core concept of the list and make the first version easier to implement and adopt.

Future revisions may have more details on each contract, such as which function calls are expected, similar to the [Yearn Allowlist](https://medium.com/iearn/yearn-allowlist-71757d4e3cf4).

# References

Content Security Policy (CSP) [https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

CORS [https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

Yearn Finance Allowlist [https://medium.com/iearn/yearn-allowlist-71757d4e3cf4](https://medium.com/iearn/yearn-allowlist-71757d4e3cf4)

Uniswap Token Lists [https://uniswap.org/blog/token-lists](https://uniswap.org/blog/token-lists)

ChainList [https://chainlist.org/](https://chainlist.org/)
