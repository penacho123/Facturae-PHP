# Firma electrónica
Aunque es posible exportar las facturas sin firmarlas, es un paso obligatorio para prácticamente cualquier trámite relacionado con la Administración Pública.
Para firmar facturas se necesita un certificado electrónico (generalmente expedido por la FNMT) del que extraer su clave pública y su clave privada.

## Firmado con clave pública y privada X.509
Si se tienen las clave pública y privada en archivos independientes se debe utilizar este método con los siguientes argumentos:
```php
$fac->sign("clave_publica.pem", "clave_privada.pem", "passphrase");
```

> #### NOTA
> Los siguientes comandos permiten extraer el certificado (clave pública) y la clave privada de un archivo PFX:
>
> ```
> openssl pkcs12 -in certificado_de_entrada.pfx -clcerts -nokeys -out clave_publica.pem
> openssl pkcs12 -in certificado_de_entrada.pfx -nocerts -out clave_privada.pem
> ```

## Firmado con PKCS#12
Desde la versión 1.0.5 de Facturae-PHP ya es posible cargar un banco de certificados desde un archivo `.pfx` o `.p12` sin necesidad de convertirlo previamente a X.509:
```php
$fac->sign("certificado.pfx", NULL, "passphrase");
```

## Fecha de la firma
Por defecto, al firmar una factura se utilizan la fecha y hora actuales como sello de tiempo. Si se quiere indicar otro valor, se debe utilizar el siguiente método:
```php
$fac->setSignTime("2017-01-01T12:34:56+02:00");
```

> #### NOTA
> Cambiar manualmente la fecha de la firma puede entrar en conflicto con el sellado de tiempo.

## Sellado de tiempo
Además de firmar las facturas, se puede añadir un sello de tiempo según el [RFC 3161](https://tools.ietf.org/html/rfc3161) para garantizar su validez durante unos meses o incluso años. Para ello, se debe llamar al siguiente método antes de exportar la factura:
```php
$fac->setTimestampServer("https://freetsa.org/tsr");
```

En caso de necesitar autenticarse con el servidor TSA se deben pasar el usuario y contraseña como parámetros:
```php
$fac->setTimestampServer("https://www.safestamper.com/tsa", "usuario", "contraseña");
```
