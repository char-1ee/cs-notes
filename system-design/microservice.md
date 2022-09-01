---
description: A typical microservice architecture.
---

# Microservice

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

#### What are mian components?

* **Load Balancer:** This distributes incoming traffic across multiple backend services.
* **CDN (Content Delivery Network)**: CDN is a group of geographically distributed servers that hold static content for faster delivery. The clients look for content in CDN first, then progress to backend services.
* **API Gateway**: This handles incoming requests and routes them to the relevant services. It talks to the identity provider and service discovery.
* **Identity Provider**: This handles authentication and authorization for user
* **Service** **Registry & Discovery**: Microservice registration and discovery happen in this component, and the API gateway looks for relevant services in this component to talk to.
* **Management**: This component is responsible for monitoring the services.
* **Microservices**: Microservices are designed and deployed in different domains. Each domain has its own database. The API gateway talks to the microservices via REST API or other protocols, and the microservices within the same domain talk to each other using RPC (Remote Procedure Call).

#### What are microservices architecture advatanges?

* Quickly designed, deployed and horizontally scaled.
* Each domain can be independently maintained by a dedicated team.
* Business requirments can be customized in each domain and better supported.

<figure><img src="https://lh5.googleusercontent.com/axSRzD0no2BNCYASAcDWXrvVpM1XcSL80mP4WQ0xs8P6vksJgMfbjoP70fsBmOLGm7kDTyRnUOumzuRqBkUm678p9UXHaIfdkQUnwG77GMghUb9qsMB5II4AFHK7NPjJsH9Kx44vhWj5k1cD8X9UiLo" alt=""><figcaption></figcaption></figure>
