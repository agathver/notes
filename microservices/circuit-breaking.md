# Circuit Breaker

Microservices often depend on other microservices to perform useful
work. Each time they serve a request they synchronously call other
microservices and construct a response by combining them.

There is always a possiblity that those microservice dependencies
are unavailable or are exibit a very high latency to be considered
usable. Not handling these gracefully will block useful system
resources like threads which will eventually cause this microservice
to degrade its performance and thus cascade this failure to dependent
microservices.

To prevent such cascading failures, we use Circuit Breaker pattern

The circuit breaker pattern works like an electrical circuit breaker.
Requests to dependencies flow through these circuit breaker. When
everything is normal, requests flow through transparently and the
circuit is said to be closed. When the dependency starts exibiting
failures, after a threshold of consequtive failures the circuit
"trips" and all further requests to the service are instantly
rejected.

After a configurable timeout, the circuit moves to half-open,
allowing a small percentage of calls to go through. If they suceed
for another configurable time called half-open interval, then the
circuit changes back to the open state. However, if the dependency
starts failing again between the half-open interval, the circuit
breaker goes back to the closed state.

```mermaid stateDiagram
  O: Open
  C: Closed H
  O: Half Open
	O --> O : Normal Operation
  O --> C : Failures > Threshold
	C --> HO : After circuit-breaker timeout HO --> C : More failures within half-open timeout
  HO --> O : No failures in half-open timeout
```

[![](https://mermaid.ink/img/eyJjb2RlIjoic3RhdGVEaWFncmFtXG4gIE86IE9wZW5cbiAgQzogQ2xvc2VkXG4gIEhPOiBIYWxmIE9wZW5cblx0TyAtLT4gTyA6IE5vcm1hbCBPcGVyYXRpb25cbiAgTyAtLT4gQyA6IEZhaWx1cmVzID4gVGhyZXNob2xkXG5cdEMgLS0-IEhPIDogQWZ0ZXIgY2lyY3VpdC1icmVha2VyIHRpbWVvdXRcblx0SE8gLS0-IEMgOiBNb3JlIGZhaWx1cmVzIHdpdGhpbiBoYWxmLW9wZW4gdGltZW91dFxuICBITyAtLT4gTyA6IE5vIGZhaWx1cmVzIGluIGhhbGYtb3BlbiB0aW1lb3V0XG5cdFx0XHRcdFx0IiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifX0)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoic3RhdGVEaWFncmFtXG4gIE86IE9wZW5cbiAgQzogQ2xvc2VkXG4gIEhPOiBIYWxmIE9wZW5cblx0TyAtLT4gTyA6IE5vcm1hbCBPcGVyYXRpb25cbiAgTyAtLT4gQyA6IEZhaWx1cmVzID4gVGhyZXNob2xkXG5cdEMgLS0-IEhPIDogQWZ0ZXIgY2lyY3VpdC1icmVha2VyIHRpbWVvdXRcblx0SE8gLS0-IEMgOiBNb3JlIGZhaWx1cmVzIHdpdGhpbiBoYWxmLW9wZW4gdGltZW91dFxuICBITyAtLT4gTyA6IE5vIGZhaWx1cmVzIGluIGhhbGYtb3BlbiB0aW1lb3V0XG5cdFx0XHRcdFx0IiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifX0)
