layout: post
title: C# Exam 40-783 Managing certificates
---

A certificate authority (CA) is a third-party issuer of certificates that is considered trust-worthy by all parties.

Locally you can run makecert to generate a testing certificate

1. Generate or obtain certificate.

2. Install certificate on your machine. (Is possible to do it on a single step: makecert -n "CN=Work" -sr currentuser -ss testCertStore)

3. To use it to sing and verify

```cs

//First we need to load it
X509Store my = new X509Store("testCertStore", StoreLocation.currentuser);
my.Open(OpenFlags.ReadOnly)Managing

//We get the certificate
X509Certificate2 cert = my.Certificates[0];

//We can sign anything with the private key
var private = (RSACryptoServiceProvider)cert.PrivateKey;

var hash = HashDataUsingSHA1(string text);
private.SignHash(hash, CryptoConfig.MapNameToOID("SHA1"))

//To veryfy we use the public key
var public = (RSACryptoServiceProvider)cert.PublicKey.Key;

```