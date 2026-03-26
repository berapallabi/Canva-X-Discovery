# Section 5: Regulatory & Compliance Environment

## Platform Policy Considerations

1. **X Developer Policy**: Third-party apps must not replicate core X functionality (e.g., building a full X client is prohibited). However, **publish-only integrations** are explicitly permitted — Buffer, Hootsuite, and Sprout Social all operate under this model.
2. **Canva App Marketplace Rules**: Publish extensions must pass Canva's App Review process. Listing guidelines require meeting Canva UI/UX standards and security policies.
3. **OAuth Token Management**: User-level OAuth 2.0 tokens must be handled securely — tokens should never be stored unencrypted; refresh token rotation must be implemented.
4. **GDPR / Data Privacy**: OAuth tokens and user design metadata must not be retained without consent. X's API terms restrict storing posts beyond 30 days without additional permission.
5. **Rate Limiting Compliance**: Implementations must respect X's per-endpoint, per-user rate limits (within 15-minute windows) to avoid API suspension.

---
