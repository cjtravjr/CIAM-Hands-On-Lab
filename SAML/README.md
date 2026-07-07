# Lab 1: Federated Identity Lifecycle Management via SAML 2.0

## Project Overview
In this lab, I built and configured an end-to-end Single Sign-On (SSO) connection using the SAML 2.0 protocol. I utilized **Auth0** as the Identity Provider (IdP) to act as the centralized user directory, and integrated it with an external Service Provider (SP), **samlsp.com**. 

By executing an automated metadata exchange containing cryptographic certificates and binding endpoints, I established a secure trust relationship between both entities. 

## The Core Purpose & Business Value of SAML
The fundamental purpose of implementing a SAML architecture is to centralize user data and credentials for authentication. This design provides massive benefits to both the enterprise and the end-user:
* **For the Organization:** It removes manual credential management. It allows companies to remain highly organized by providing a centralized database to audit exactly who has active access. This centralized visibility allows IT and Identity teams to execute rapid security actions, such as revoking access, resetting logins, and viewing active user sessions instantly from one console.
* **For the User:** It eliminates password fatigue and risk by removing the need to remember multiple credentials across various separate third-party applications.
