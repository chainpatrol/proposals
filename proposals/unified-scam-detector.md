# Unified Scam Detector [Draft]

### Draft

<aside>
🚧 This document is a work in progress, we are gathering feedback and suggestions to arrive at a complete specification.

</aside>

### Abstract

### Inspiration

# Problem Definition

A crypto wallet is where you can securely store your crypto assets. Typically it's a software that you install
on your computer as a desktop application, as a browser extension, or as a mobile application. At a minimum, it
allows you to send and receive crypto assets. However, because wallets are the primary entry point for most users
to the crypto ecosystem, users expect a certain level of security and safety. In particular, we've seen that when
users are taken advantage of by scammers and hackers, it's often the wallet that is blamed first for not providing
adequate warnings and recovery mechanisms to the user.

To mediate this issue, wallet providers have started to implement their own scam detection mechanisms. Many using
a whitelist/blacklist approach, where they maintain a list of known scam sites and warn users when they try to
interact with them (See [eth-phishing-detect](https://github.com/MetaMask/eth-phishing-detect), [PhishFort](), [Polkadot](https://github.com/polkadot-js/phishing), [Infinity wallet](https://www.npmjs.com/package/@infinitywallet/crypto-phishing-detector), etc.).

# Proposed Solution

# Technical Details

# Examples

```js
import { ScamDetector } from "@chainpatrol/scam-detector";
import {
  EthPhishingDetectSource,
  PhishFortSource,
  GoogleSafeSearchSource,
} from "@chainpatrol/scam-detector/sources";

const detector = ScamDetector.init({
  sources: [EthPhishingDetectSource, PhishFortSource, GoogleSafeSearchSource],
});

// Check
detector.check("https://scam-site.com");
detector.check("0x123124324214");
detector.check("<html><head></head><body></body></html>");

// Report
detector.report("https://new-site.ca");
detector.report(); // If no URL provided, collect metadata from window.location
```

# Wallet implementation

# Open Questions

# FAQ and Decisions

# References
