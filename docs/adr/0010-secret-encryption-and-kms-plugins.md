# ADR-0010: Encrypt Stored Secrets, with KMS Through the Kubernetes KMS v2 Plugin Protocol

* **Status:** Accepted
* **Date:** 2026-11-17

## Context

Registering credentials in the admin UI puts secrets in PostgreSQL: APNs `.p8` keys, FCM service account JSON, TOTP secrets, and the OIDC client secret. API keys are stored as hashes and need no encryption. Some users require keys managed in a KMS, but supporting each cloud's KMS in push-hub would add cloud-specific code, which the design principles exclude.

## Decision

* **Envelope encryption:** each secret is encrypted with its own data key using AES-256-GCM, and the data key is wrapped by a key-encryption key.
* **Default key-encryption key:** a master key passed through an environment variable or a mounted file.
* **KMS:** push-hub includes a client for the **Kubernetes KMS v2 plugin protocol** (gRPC over a Unix socket: `Status`, `Encrypt`, `Decrypt`). Users run a KMS plugin as a sidecar and point push-hub to its socket. push-hub contains no cloud-specific KMS code.
* **Rotation:** each ciphertext records the ID of the key that wrapped it. A CLI command re-wraps all data keys.
* **Secrets outside the database:** credentials can still be passed through environment variables or files. The UI then shows them as externally managed and read-only.
* **Fail closed:** push-hub refuses to start if encrypted secrets exist and no key-encryption key is available.
* **Lost keys cannot be recovered.** The documentation describes how to register the secrets again.

## Alternatives Considered

* **Built-in AWS KMS support only:** suggests that push-hub is AWS-specific.
* **Built-in support for each KMS:** too much code to maintain, and each user needs a different one.
* **A Go plugin interface:** users would have to fork and rebuild the single binary.

## Consequences

* Existing KMS plugins for kube-apiserver may work with push-hub without changes. This is **not yet verified**. Verifying at least the AWS plugin is part of the v1.0 quality bar ([ADR-0012](0012-v1-quality-bar-and-test-strategy.md)).
* Third-party plugins are documented as tested examples, not as supported components.
* Users can write their own plugin in any language, since the protocol has three calls.

## Evidence

* Protocol: [`staging/src/k8s.io/kms/apis/v2/api.proto`](https://github.com/kubernetes/kubernetes/blob/master/staging/src/k8s.io/kms/apis/v2/api.proto) defines `Status`, `Decrypt`, and `Encrypt`. `EncryptResponse` returns `ciphertext` and `key_id`.
* Existing plugins with KMS v2 support (checked 2026-11-17):
  * [kubernetes-sigs/aws-encryption-provider](https://github.com/kubernetes-sigs/aws-encryption-provider): `pkg/plugin/plugin_v2.go` (231 stars, pushed 2026-10-23)
  * [Azure/kubernetes-kms](https://github.com/Azure/kubernetes-kms): `pkg/plugin/kms_v2_server.go` (181 stars, pushed 2026-07-27)
  * [GoogleCloudPlatform/k8s-cloudkms-plugin](https://github.com/GoogleCloudPlatform/k8s-cloudkms-plugin): README shows `apiVersion: v2` (57 stars, pushed 2026-07-25)
