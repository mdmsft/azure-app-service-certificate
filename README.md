# View certificate order
```sh
az resource show --name $CERTIFICATE_NAME --resource-group $RESOURCE_GROUP_NAME --resource-type "Microsoft.CertificateRegistration/certificateOrders"
```

# Extract certificate full chain
```sh
az resource show --name $CERTIFICATE_NAME --resource-group $RESOURCE_GROUP_NAME --resource-type "Microsoft.CertificateRegistration/certificateOrders" --query 'properties | [signedCertificate.rawData, intermediate.rawData, root.rawData]' -o tsv
```

# Extract only certificate
```sh
az resource show --name $CERTIFICATE_NAME --resource-group $RESOURCE_GROUP_NAME --resource-type "Microsoft.CertificateRegistration/certificateOrders" --query 'properties.signedCertificate.rawData' -o tsv
```

# Download as secret
```sh
az keyvault secret download -n $(az resource show --name $CERTIFICATE_NAME --resource-group $RESOURCE_GROUP_NAME --resource-type "Microsoft.CertificateRegistration/certificateOrders" --query 'properties.certificates.$CERTIFICATE_NAME.keyVaultSecretName' -o tsv) --vault-name $VAULT_NAME -f tls.pfx -e base64
```

# Extract certificate chain
```sh
openssl pkcs12 -in tls.pfx -cacerts -nokeys | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p'
```

# Extract certificate only
```sh
openssl pkcs12 -in tls.pfx -clcerts -nokeys | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p'
```

# Extract private key
```sh
openssl pkcs12 -in tls.pfx -nocerts -nodes | sed -ne '/-BEGIN PRIVATE KEY-/,/-END PRIVATE KEY-/p'
```
