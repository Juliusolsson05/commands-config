---
description: Stop AI from reinventing the wheel — use existing libraries properly
---

## The AI Creativity Issue

AI agents are trained to be helpful problem-solvers, but that training often pushes them toward over-engineering. When you give them a simple request — "call this API and handle the response" — they frequently ignore mature, battle-tested libraries and instead start building a brand-new wrapper, custom client class, or mini-framework "for robustness."

The result is the same every time: extra files, custom retry logic, homemade auth layers, and exception hierarchies that duplicate what the community has already solved perfectly. This isn't creativity; it's unnecessary reinvention that bloats the codebase and creates long-term maintainer debt.

### What the AI usually generates
```python
# AI's "robust" solution — a full custom client from scratch
class EnterpriseApiClient:
    def __init__(self, base_url, api_key):
        self.session = self._create_resilient_session()
        self.auth = CustomOAuthHandler(api_key)
        self.retry_policy = SmartBackoffStrategy(max_retries=5)

    async def call(self, endpoint, payload=None, method="GET"):
        # 150+ lines of custom circuit-breaker, telemetry,
        # response transformation, and error classification...
        ...
```

This looks professional, but it's reinventing the wheel in the worst way.

### The correct approach
Sometimes you *do* need robust configuration: retries, timeouts, authentication, rate-limiting, logging, and graceful error handling. That's completely valid. The key is to reach for the libraries the entire community has already refined for exactly these problems instead of writing your own.

**Python example (using the de-facto standard library everyone actually uses):**
```python
import requests
from requests.adapters import HTTPAdapter
from requests.packages.urllib3.util.retry import Retry

def get_robust_session() -> requests.Session:
    """One-time setup for a properly configured, battle-tested client."""
    session = requests.Session()
    
    retry_strategy = Retry(
        total=5,
        backoff_factor=1,
        status_forcelist=[429, 500, 502, 503, 504]
    )
    adapter = HTTPAdapter(max_retries=retry_strategy)
    session.mount("http://", adapter)
    session.mount("https://", adapter)
    
    session.headers.update({"User-Agent": "MyApp/1.0"})
    return session

# Usage — clean, simple, zero custom wrappers
session = get_robust_session()
response = session.get("https://api.example.com/data", timeout=10)
response.raise_for_status()
data = response.json()
```

**JavaScript example (Axios — the community standard):**
```js
import axios from 'axios';

const api = axios.create({
  baseURL: 'https://api.example.com',
  timeout: 10000,
  headers: { 'User-Agent': 'MyApp/1.0' },
  validateStatus: status => status < 500   // only treat 5xx as errors
});

// Axios already ships with interceptors, retry plugins, and adapters
api.interceptors.response.use(
  response => response,
  error => { /* one shared error handler */ }
);
```

### Why the AI keeps reinventing the wheel
- It was rewarded in training for producing "complete" and "production-ready" looking code.
- It doesn't inherently know which libraries are the accepted standard in your ecosystem.
- Creating new code feels more impressive than a one-line `import requests` or `import axios`.
- The long-term cost of maintenance is invisible to the model at generation time.

### The rule that fixes it
**Never write your own HTTP client, auth wrapper, retry engine, or similar unless you have a genuinely unique requirement that no existing library satisfies.**

Mature libraries like:
- `requests` + `urllib3` (Python)
- Axios (JavaScript)
- Fetch + `ky` or `ofetch` (modern JS)
- `http4s`, `okhttp`, `retrofit` (JVM), etc.

exist for a reason. They are battle-tested by millions of developers, receive security updates, and are actively maintained. Using them with proper configuration gives you all the robustness you actually need — without the hidden cost of maintaining yet another custom abstraction.

When an AI starts inventing a new client class, stop it immediately and ask:
"Can this be solved by configuring an existing, widely-adopted library instead of writing our own?"

If the answer is yes, rewrite it that way. That single habit is one of the highest-leverage ways to keep a codebase clean, maintainable, and free of self-inflicted tech debt.

$ARGUMENTS
