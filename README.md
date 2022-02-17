
# 0. Stamping CFDI 4.0

The REST stamping services from Syncfy let you emit invoices [CFDI's](https://www.sat.gob.mx/consultas/43074/actualizacion-factura-electronica---reforma-fiscal-2022-)for the new 4.0 regulations . This services allow you to:

1. Stamp invoices though the SAT with 4.0 regulation
2. Cancel invoices previouly stamped throught Syncfy.
3. Get the invoices stamped through the services


## Tha main changes of the 4.0 version are:
- The next data fields are now required from issuer and recipient:
    -   Fiscal Regime (RegimenFiscal)
    -   Name (Nombre)
    -   Fiscal Address (DomicilioFiscal) only required for the recipient
- The issuer and recipient data must match with the register that the SAT have
- The name must match exactly as you are registered on the SAT and it must match with the certificates
- It now required to state if the invoices is for exportation on with the field: "Exportacion"
- Now a global CFDI can be emitted to report operations for general public (gloabl cfdi)
- The invoice cancellation now require to add a reason for cancellation code.


# 1. Taxpayer

## Add new Taxpayer

```
POST invoicing/mx/taxpayers
```

The  first step to create a CFDI is add a new taxpayer to the system, each Syncfy user can have multiple taxpayers. To add a new taxpayer is needed:

- RFC of the taxpayer (TaxId eg. PEF150508S62).
- Credentials [CSD](https://www.sat.gob.mx/tramites/17507/envia-la-solicitud-para-tu-certificado-de-sello-digital-para-emitir-facturas-electronicas). This credentials are generated from the eFirma that the SAT emit to each taxpayer. The CSD are two files cer and key which must be code to base64 to send on the request.
- Password of the CSDs. 
- Syncfy - API key.
- Syncfy - User  (id_user).

Note: The folder '/CSD' includes sandbox CSDs that you can use safely to test the process, the invoices generated with this credentials will not be registered on the SAT.


This is a body example of the request to register a new Taxpayer using the sandbox CSD.

```
{
    "api_key" : "{{sync_api_key}}",
    "id_user" : "{{sync_id_user}}",
    "taxpayer" : "EKU9003173C9",
    "password" : "12345678a",
   "cer": "MIIFuzCCA6OgAwIBAgIUMzAwMDEwMDAwMDA0MDAwMDI0MzQwDQYJKoZIhvcNAQELBQAwggErMQ8wDQYDVQQDDAZBQyBVQVQxLjAsBgNVBAoMJVNFUlZJQ0lPIERFIEFETUlOSVNUUkFDSU9OIFRSSUJVVEFSSUExGjAYBgNVBAsMEVNBVC1JRVMgQXV0aG9yaXR5MSgwJgYJKoZIhvcNAQkBFhlvc2Nhci5tYXJ0aW5lekBzYXQuZ29iLm14MR0wGwYDVQQJDBQzcmEgY2VycmFkYSBkZSBjYWRpejEOMAwGA1UEEQwFMDYzNzAxCzAJBgNVBAYTAk1YMRkwFwYDVQQIDBBDSVVEQUQgREUgTUVYSUNPMREwDwYDVQQHDAhDT1lPQUNBTjERMA8GA1UELRMIMi41LjQuNDUxJTAjBgkqhkiG9w0BCQITFnJlc3BvbnNhYmxlOiBBQ0RNQS1TQVQwHhcNMTkwNjE3MTk0NDE0WhcNMjMwNjE3MTk0NDE0WjCB4jEnMCUGA1UEAxMeRVNDVUVMQSBLRU1QRVIgVVJHQVRFIFNBIERFIENWMScwJQYDVQQpEx5FU0NVRUxBIEtFTVBFUiBVUkdBVEUgU0EgREUgQ1YxJzAlBgNVBAoTHkVTQ1VFTEEgS0VNUEVSIFVSR0FURSBTQSBERSBDVjElMCMGA1UELRMcRUtVOTAwMzE3M0M5IC8gWElRQjg5MTExNlFFNDEeMBwGA1UEBRMVIC8gWElRQjg5MTExNk1HUk1aUjA1MR4wHAYDVQQLExVFc2N1ZWxhIEtlbXBlciBVcmdhdGUwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCN0peKpgfOL75iYRv1fqq+oVYsLPVUR/GibYmGKc9InHFy5lYF6OTYjnIIvmkOdRobbGlCUxORX/tLsl8Ya9gm6Yo7hHnODRBIDup3GISFzB/96R9K/MzYQOcscMIoBDARaycnLvy7FlMvO7/rlVnsSARxZRO8Kz8Zkksj2zpeYpjZIya/369+oGqQk1cTRkHo59JvJ4Tfbk/3iIyf4H/Ini9nBe9cYWo0MnKob7DDt/vsdi5tA8mMtA953LapNyCZIDCRQQlUGNgDqY9/8F5mUvVgkcczsIgGdvf9vMQPSf3jjCiKj7j6ucxl1+FwJWmbvgNmiaUR/0q4m2rm78lFAgMBAAGjHTAbMAwGA1UdEwEB/wQCMAAwCwYDVR0PBAQDAgbAMA0GCSqGSIb3DQEBCwUAA4ICAQBcpj1TjT4jiinIujIdAlFzE6kRwYJCnDG08zSp4kSnShjxADGEXH2chehKMV0FY7c4njA5eDGdA/G2OCTPvF5rpeCZP5Dw504RZkYDl2suRz+wa1sNBVpbnBJEK0fQcN3IftBwsgNFdFhUtCyw3lus1SSJbPxjLHS6FcZZ51YSeIfcNXOAuTqdimusaXq15GrSrCOkM6n2jfj2sMJYM2HXaXJ6rGTEgYmhYdwxWtil6RfZB+fGQ/H9I9WLnl4KTZUS6C9+NLHh4FPDhSk19fpS2S/56aqgFoGAkXAYt9Fy5ECaPcULIfJ1DEbsXKyRdCv3JY89+0MNkOdaDnsemS2o5Gl08zI4iYtt3L40gAZ60NPh31kVLnYNsmvfNxYyKp+AeJtDHyW9w7ftM0Hoi+BuRmcAQSKFV3pk8j51la+jrRBrAUv8blbRcQ5BiZUwJzHFEKIwTsRGoRyEx96sNnB03n6GTwjIGz92SmLdNl95r9rkvp+2m4S6q1lPuXaFg7DGBrXWC8iyqeWE2iobdwIIuXPTMVqQb12m1dAkJVRO5NdHnP/MpqOvOgLqoZBNHGyBg4Gqm4sCJHCxA1c8Elfa2RQTCk0tAzllL4vOnI1GHkGJn65xokGsaU4B4D36xh7eWrfj4/pgWHmtoDAYa8wzSwo2GVCZOs+mtEgOQB91/g==",
  "key": "MIIFDjBABgkqhkiG9w0BBQ0wMzAbBgkqhkiG9w0BBQwwDgQIAgEAAoIBAQACAggAMBQGCCqGSIb3DQMHBAgwggS8AgEAMASCBMh4EHl7aNSCaMDA1VlRoXCZ5UUmqErAbucRFLOMmsAaFNkyWR0dXIAh0CMjE6NpQIMZhQ0HH/4tHgmwh4kCawGjIwERoG6/IH3mCt7u19J5+m6gUEGOJdEMXj976E5lKCd/EG6t6lCq66GE3rgux/nFmeQZvsjLlzPyhe2j+X81LrGudITTjDdgLI0EdbdV9CUJwWbibzrVxjuAVShRh07XPL/DiEw3Wk2+kdy4cfWmMvh0U55p0RKZopNkWuVVSvr3ai7ZNCwHZWDVqkUDpwDDGdyt0kYQ7qoKanIxv/A9wv6ekq0LQ/yLlOcelkxQeb8Glu4RXe+krRvrASw1eBAQ3mvNKpngwF8vtlyoil41PjHUOKALMJtNpywckRRYOk4703ylWIzTfdBlrZ6VmDBjdC5723G1HAx3R/x+o+08++RNiFaN06Ly5QbZZvjnealDfSKz1VKRHWeXggaW87rl4n0SOOWnvabKs4ZWRXTS0dhWK+KD/yYYQypTslDSXQrmyMkpc1Zcb4p9RTjodXxGCWdsR5i5+Ro/RiJvxWwwaO3YW6eaSavV0ROqANQ+A+GizMlxsVjl6G5Ooh6ORdA7jTNWmK44Icgyz6QFNh+J3NibxVK2GZxsQRi+N3HXeKYtq5SDXARA0BsaJQzYfDotA9LFgmFKg9jVhtcc1V3rtpaJ5sab8tdBTPPyN/XT8fA0GxlIX+hjLd3E9wB7qzNR6PZ84UKDxhCGWrLuIoSzuCbr+TD9UCJprsfTu8kr8Pur4rrxm7Zu1MsJRR9U5Ut+O9FZfw4SqGykyTGGh0v1gDG8esKpTW5MKNk9dRwDNHEmIF6tE6NeXDlzovf8VW6z9JA6AVUkgiFjDvLUY5MgyTqPB9RJNMSAZBzrkZgXyHlmFz2rvPqQGFbAtukjeRNS+nkVayLqfQnqpgthBvsgDUgFn03z0U2Svb094Q5XHMeQ4KM/nMWTEUC+8cybYhwVklJU7FBl9nzs66wkMZpViIrVWwSB2k9R1r/ZQcmeL+LR+WwgCtRs4It1rNVkxXwYHjsFM2Ce46TWhbVMF/h7Ap4lOTS15EHC8RvIBBcR2w1iJ+3pXiMeihArTELVnQsS31X3kxbBp3dGvLvW7PxDlwwdUQOXnMoimUCI/h0uPdSRULPAQHgSp9+TwqI0Uswb7cEiXnN8PySN5Tk109CYJjKqCxtuXu+oOeQV2I/0knQLd2zol+yIzNLj5a/HvyN+kOhIGi6TrFThuiVbbtnTtRM1CzKtFGuw5lYrwskkkvenoSLNY0N85QCU8ugjc3Bw4JZ9jNrDUaJ1Vb5/+1GQx/q/Dbxnl+FK6wMLjXy5JdFDeQyjBEBqndQxrs9cM5xBnl6AYs2Xymydafm2qK0cEDzwOPMpVcKU8sXS/AHvtgsn+rjMzW0wrQblWE0Ht/74GgfCj4diCDtzxQ0ggi6yJD+yhLZtVVqmKS3Gwnj9RxPLNfpgzPP01eYyBBi/W0RWTzcTb8iMxWX52MTU0oX9//4I7CAPXn0ZhpWAAIvUmkfjwfEModH7iwwaNtZFlT2rlzeshbP++UCEtqbwvveDRhmr5sMYkl+duEOca5156fcRy4tQ8Y3moNcKFKzHGMenShEIHz+W5KE="
}
```


## Update Taxpayer information

The data from the CSD's for each taxpayer can be updatedthrough the next endpoint .

```
PUT invoicing/mx/taxpayers
```



## Get list of Taxpayer

You can retrieve the list of the Taxpayer already added to the system though the next endpoint:

```
GET invoicing/mx/taxpayers
```
The response is an array of object each one contains the RFC of the taxpayer, and the range of dates on UNIX timestamp that indicates the validity of the certificates.

Response example:
```
{
    "rid": "5cdb1520-cfbf-4d72-98d2-XXXXXXXX",
    "code": 200,
    "errors": null,
    "status": true,
    "message": null,
    "response": [
        {
            "taxpayer": "AAA010101AAA",
            "cerValidFrom": 1495079696,
            "cerValidTo": 1621310096
        },
        {
            "taxpayer": "GOYA780416GM0",
            "cerValidFrom": 1351012593,
            "cerValidTo": 1477242993
        },
        {
            "taxpayer": "EKU9003173C9",
            "cerValidFrom": 1477432331,
            "cerValidTo": 1603662731
        }
    ]
}
```
# 2. Invoices

## Stamp Invoices

### Stamp from XML

You can stamp invoices sending the XML structure according the [SAT regulation](http://omawww.sat.gob.mx/tramitesyservicios/Paginas/documentos/GuiallenadoCFDIglobal311221.pdf)

To stamp you must send the data of the XML converted to a base64 string:

```
curl --location --request POST 'https://sync.paybook.com/v1/invoicing/mx/invoices' \
--header 'Content-Type: application/json' \
--data-raw '{
    "api_key": "{{api_key}}",
    "id_user": "{{id_user}}",
    "invoice_xml": "{{invoice_xml_base64}}"
}'
```

Examples of 4.0 Structures:
- [Factura Ingreso 4.0 XML](/XML/Ingreso.xml) 
- [Factura Egreso 4.0 XML](/XML/Egreso.xml) 
- [Factura Ingreso Global 4.0 XML](/XML/Global.xml) 
- [Factura Ingreso 4.0 XML](/XML/Ingreso.xml) 
- [Factura Ingreso 4.0 XML](/XML/Ingreso.xml) 
Note: This  XML structures need to encoded to base64 before sending on the request


### Stamp from JSON
You can generate the invoice with a JSON structure, and we will trasnform it to the aproppiate XML structures and stamp it, here is an example of how to stam a JSON invoice:

```curl --location --request POST 'https://sync.paybook.com/v1/invoicing/mx/invoices' \
--header 'Content-Type: application/json' \
--data-raw '{
    "id_user": "{{id_user}}",
    "api_key": "{{api_key}}",
    "invoice_data": {
        "Version": "4.0",
        "Serie": "A",
        "Folio": "1",
        "Fecha": "2022-02-13T14:12:11",
        "FormaPago": "99",
        "SubTotal": "1000.00",
        "Moneda": "MXN",
        "Total": "1000.00",
        "TipoDeComprobante": "I",
        "MetodoPago": "PUE",
        "LugarExpedicion": "45428",
        "NumCtaPago": "NO IDENTIFICADA",
        "Descuento": "0.00",
        "TipoCambio": "1",
        "Exportacion": "01",
        "Emisor": {
            "Rfc": "EKU9003173C9",
            "Nombre": "ESCUELA KEMPER URGATE SA DE CV",
            "RegimenFiscal": "603"
        },
        "Receptor": {
            "Rfc": "XOJI740919U48",
            "Nombre": "INGRID XODAR JIMENEZ",
            "DomicilioFiscalReceptor": "88965",
            "RegimenFiscalReceptor": "621",
            "UsoCFDI": "G03"
        },
        "Conceptos": [
            {
                "ClaveProdServ": "85121502",
                "NoIdentificacion": "None",
                "Cantidad": "1.0",
                "ClaveUnidad": "E48",
                "Unidad": "Unidad de Servicio",
                "Descripcion": "Consulta médica",
                "ValorUnitario": "1000.00",
                "Importe": "1000.00",
                "Descuento": "0.00",
                "ObjetoImp": "01"
            }
        ]
    }
}'
```

Examples of 4.0 Structures:
- [Factura Ingreso 4.0 JSON](/JSON/ingreso1.json)
- [Factura Egreso 4.0 JSON](/JSON/Egreso.json)
- [Factura Ingreso Sin Impuestos 4.0 JSON](/JSON/IngresoSinImpuestos.json)
- [Factura Traslado 4.0 JSON](/JSON/Traslado.json)
- [Factura Pagos 4.0 JSON](/JSON/Pagos.json)
- [Factura Nomina 4.0 JSON](/JSON/Nomina.json)


### Stamping response

Both methods will return the same response that containes the next:
```
    {
    "rid": "40080197-bf74-4101-9278-f35f2d6b4208",
    "code": 200,
    "errors": null,
    "status": true,
    "message": null,
    "response": {
        "taxpayer": "EKU9003173C9",
        "errors": [], // Errors as an arrray of strings
        "success": true,
        "xml": "<?xml version=\"1.0\" encoding=\"utf-8\"?><cfdi:Comprobante...", // the full XML escaped string of the CFDI
        "uuid": "ADE28016-7A79-443B-B285-77BBE86ABCF8", // UUID SAT identifier
        "cadenaOriginal": "||1.1|ade28016-7a79-443b-b285-77bbe86abcf8|2022-02-15T20:26:56|SPR190613I52|Rw+hLFp6kgmn+VOSOMRoJSqicdI/x4kOiPfgQRQabONeI9KjDiQJNahAPlO02LqDt8pnuXK6AXNeKICHQzhBoCRnx8mutWJ/7PMdplAe+mMdJoBZ0aUnoNPPw1Ix7ysqZPzIhtn6g2IWyJaDhVvWfkZiDOZpHD9HvgsQUrSacVcXxGgs1oWZl4+FSOEWCwHS0zgHci9DqDe1pnzN1ZKXUAXLn3HhuwOprWGC0tauwuk5SGcSSym3+66I7rx8m38SbWBvO2JcAPgeWAKKnAkYuVO0vQcNw2N1M52SUqADxB8K0PKteLaqPb65caShlCVjmtY6Dff3lPFIEVuOS/uLQQ==|30001000000400002495||",
        "selloSAT": "OSqmqjkY6HajweYvJM/CdFoBLwYSPopnt/nS26usvXdRB6XwqgzEVudnWy9/WCPm6arIlxSHTWPdNLPM4YQMprgzhQnRRaNX2LdMoRP9kWsRhN4b+d47JGVpP8LBhYBgads1RFBBxHhUH8zZGUekxolsAizYfxDpoez8kM0AlzYBUoWnJW4/E9VSrNDDxh/eZQfD44Jv3H2mOA4YznZOaXhHG8fNQQWSv+RmM5FMneu803MkRhB/B2DvBW75Q52hS665UkDlNlR5gRGfs5xY/264cmPYc7eQz7UCrebxfrQOm604BmKkvecvG1eziXYMjzV72QY+Un1Mk4UPWVRklQ==",
        "selloCFD": "Rw+hLFp6kgmn+VOSOMRoJSqicdI/x4kOiPfgQRQabONeI9KjDiQJNahAPlO02LqDt8pnuXK6AXNeKICHQzhBoCRnx8mutWJ/7PMdplAe+mMdJoBZ0aUnoNPPw1Ix7ysqZPzIhtn6g2IWyJaDhVvWfkZiDOZpHD9HvgsQUrSacVcXxGgs1oWZl4+FSOEWCwHS0zgHci9DqDe1pnzN1ZKXUAXLn3HhuwOprWGC0tauwuk5SGcSSym3+66I7rx8m38SbWBvO2JcAPgeWAKKnAkYuVO0vQcNw2N1M52SUqADxB8K0PKteLaqPb65caShlCVjmtY6Dff3lPFIEVuOS/uLQQ=="
    }
}
```


## Get Invoices

You can get previously stamped invoices using the next endpoint:

```
curl --location --request GET 'https://sync.paybook.com/v1/invoicing/mx/invoices?api_key={{api_key}}&id_user={{id_user}}'
```
You can query the invoices sending some of the next parameters on the request:
```
{
    uuid: SAT unique identifier,
    issuer: Taxpayer code of the issuer,
    recipient: Taxpayer of the recipient,
    dt_create_from: From date when the invoice was stamped  UNIX_timestamp,
    dt_create_to: To date when the invoice was stamped  UNIX_timestamp,
    dt_register_from: From date for the invoice register (Fecha)  UNIX_timestamp,
    dt_register_to: To date for the invoice register (Fecha)  UNIX_timestamp,
}
```
For example if you want to query all the invoices issued by certain taxpayer on date range

```
curl --location --request GET 'https://sync.paybook.com/v1/invoicing/mx/invoices?api_key={{api_key}}&id_user={{id_user}}{{id_user}}&issuer=EKU9003173C9&dt_create_from=1644928742&dt_create_to=1645015142
```

Note: You can get up to 20 invoices for each GET request
Note: We store the stamped invoices for 24 months

### Cancel Invoices

You can cancel previously stamped invoices on syncfy using the next request:

```
curl --location --request PUT 'https://sync.paybook.int/v1/invoicing/mx/invoices/{{invoice_uuid}}/cancel' \
--header 'Content-Type: application/json' \
--data-raw '
{
"reason" : "02",
"api_key" : "{{api_key}}",
"id_user" : "{{id_user}}"
}'
```

The cancellation request requires a reason for cancellation code:
-  "01" : The CFDI have an error and it include a relation with othe CFDI
-  "02" : The CFDI have an error and it don't include a relation
-  "03" : The transaction that represent the invoice was not done
-  "04" : Operation related with a global invoice

Note: The cancelled invoices will remain stored on the system for 24 months. 

The response of cancellation is similar to this:
```
    "success": true,
    "cancel_receipt": "<?xml version=\"1.0\" encoding=\"utf-8\"?><Acuse xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" Fecha=\"2022-02-16T13:11:21.354328\" RfcEmisor=\"EKU9003173C9\"><Folios xmlns=\"http://cancelacfd.sat.gob.mx\"><UUID>CD7DC7D7-6C18-4092-B8F8-0F2589316EE1</UUID><EstatusUUID>201</EstatusUUID></Folios>...",
    "code": "201",
    "uuid": "CD7DC7D7-6C18-4092-B8F8-0F2589316EE1"
```

- cancel_receipt: This the XML that the provider return as proof of cancellation request, the structure can vary so it's recomended to store as string. It contains two important elements the UUID and the EstatusUUID, that is the response status for the cancellation request.
- code: This is the EstatusUUID that comes in the cancellation response, these are some possible response codes:
    - 201 - UUID success cancellation request.
    - 202 – UUID previously cancellation request.
    - 203 - The issuer is not owner of the UUID.
    - 204 - UUID is not aplicable for cancellation
    - 205 - UUID not found.
- success: It return true if the cancellation code is 201 or 202, else it will be false

IMPORTANT: The success on cancellation request don't assure that the invoices is cancelled, it only assure that the cancellation request is valid, the process can vary depending on the invoices, under some conditions the cancellation requires the approbation of the recipient, the current process is explained on the [SAT cancelation site ](http://omawww.sat.gob.mx/factura/Paginas/cancela_procesocancelacion.htm#)




#### Send email invoice 

When you use the  POST invoicing/mx/invoices, optionally you can send ena email invoice that contains a basic PDF files and the XML CFDI.

To send it on stamping you need to add the parameter "email_data" on the stamping request:

```
{
    "api_key" : "{{sync_api_key}}",
    "id_user" : "{{sync_id_user}}",
    "invoice_data": {} 
    "email_data": {
        "email": "emailcliente@email.com", //(required)
        "reply": "miemail@email.com", //(optional) 
        "body": "<h1> Hola Mundo</h1>", //(optional) 
        "subject": "Email de factura" //(optional)  
    }
}
```

- "email" : Is the email of the recipient
- "reply" : The email of the which will be put on the reply field (optional).
- "body" : Is the content of the mail, it can be plain text or HTML (optional)
- "subject" : Is the subject that will appear on the email (optional)

#### Resend the email invoice

You can resend a previously stamped invoice with the next endpoint:
```
POST invoicing/mx/invoices/{{uuid}}/send
```

THe required parameters are:

```
{
    "api_key": {{api_key}},
    "id_user": {{id_user}},
    "email_data": {
        "email": "client_email@domain.com", //(required)
        "reply": "support@mycompany.com", //(optional) 
        "body": "<h1> Hello world</h1>", //(optional) 
        "subject": "Here is your invoice" //(optional)  
    }
}
```

#### Add comments to the PDF

You can add some comments on the printed version of the invoice, you need to send the paramater "pdf_comments" on the stamping request:
```
{
    "api_key": la api key de paybook,
    "id_user": el id del usuario,
    "pdf_comments": "This the invoices for test sale",
    "invoice_data":  ...
}
```

