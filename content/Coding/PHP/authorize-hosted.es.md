---
title: "Forma Authorize Hosted en PHP"
description: "Código reducido en PHP para implementar forma de pago en línea con Authorize"
date: 2020-02-14T17:25:00Z
author: "Hugo Avila"
---

## Introducción

Estas instrucciones son para implementar el sistema de [Authorize Hosted](https://developer.authorize.net/api/reference/features/accept_hosted.html). Por medio del cual lograremos tener una forma de pago con tarjeta de crédito en nuestra página web. La forma que nosotros le solicitamos es generada en el sistema de [**Authorize.net**](https://authorize.net) e insertada en nuestra pagina web. En nuestro caso veremos la opción para insertar la forma en un iFrame.

**Créditos**

_La mayoría del código se ha tomado y simplificado del ejemplo oficial en GitHub [AuthorizeNet/accept-sample-app ](https://github.com/AuthorizeNet/accept-sample-app/blob/master/README-AcceptHosted.md)_

## Procedimiento

1. Crear cuenta Authorize Developer.
1. Obtener el usuario y una llave de transacción del API.
1. Preparar el servidor.
1. Solicitar token.
1. Con el token se solicitara la forma y se inyectará en un iFrame.
1. Probar pago.

### 1. Crear Cuenta Authorize Developer

Para fines de pruebas, se requiere abrir una cuenta en [Developer de Authorize.net](https://developer.authorize.net/hello_world/sandbox/). Seguir las instrucciones de registro.

### 2. Obtener Transaction Key

Para obtener las credenciales del API [acceder a su cuenta](https://sandbox.authorize.net/) de Sandbox y entrar a la opción **ACCOUNT** y **[API Credentials & Keys](https://sandbox.authorize.net/UI/themes/sandbox/User/TransactionKey.aspx)** En la sección de **Create New Key(s)** seleccionar **New Transaction Key** y dar click en Submit. Se debe copiar el numero de llave generado ya que no se volverá a mostrar.

### 3. Preparar el Servidor

El servidor web debe contar con un certificado de seguridad para utilizar el script Accept.js. Para un servidor local (localhost) aquí encuentran [instrucciones para configurar Apache y crear un certificado SSL auto-firmado](https://getgrav.org/blog/macos-catalina-apache-ssl) para Mac.

En un un servidor publico, si cuenta con cPanel, la mayoría ya ofrece certificados gratuitos, si no se tine la opción, se puede [obtener un certificado gratuito en Zero SSL](https://zerossl.com/), seguir las instrucciones para realizar la instalación.

Un método recomendable para no exponer los datos de la llave, es almacenarlos en una variable de environment `ENV_VAR` y requerirla en el script PHP cuando sea necesario. Esta variable se puede guardar en un archivo `.htaccess` , por ejemplo:

```
SetEnv API_LOGIN_ID 123456ABCD
SetEnv TRANSACTION_KEY 123456789ABCDEFGHIJ
```

y en PHP obtenerlas con:

```php
<?php
getenv('TRANSACTION_KEY');
getenv('API_LOGIN_ID');
```

Si se obtiene un error 500 o no funcionan las variables, puede ser que el servidor no tenga habilitado el uso de variables de entorno, para habilitarlo en **Apache** se debe editar el archivo `httpd.conf`, quitar el `#` al renglón del `env_module` y reiniciar el servidor.

```conf
LoadModule env_module lib/httpd/modules/mod_env.so
```

### 4. Solicitar el Token

Para obtener el token se deben enviar los parámetros de forma en XML (o JSON). En el siguiente ejemplo ocurre lo siguiente:

1. Activamos el despliegue de errores de PHP para poder saber que pasa en caso de problemas. Escribimos unos datos ejemplo para el producto a pagar y manejamos las solicitudes de "Cancel" y "Return".
1. Generamos el string en XML conforme a los requerimientos, en [Authorize.net sección de Hosted Form Parameter Settings](https://developer.authorize.net/api/reference/features/accept_hosted.html) esta la información detallada.
1. Creamos el objeto XML
1. Adicionamos los datos que son generalmente dinámicos, como las credenciales del API, los datos del producto y las url de retorno.
1. Se realiza el envío de la forma tipo POST al API de Authorize.
1. Si todo esta bien, contestara con "Ok" acompañado del token.

El siguiente código de ejemplo lo guardamos en un archivo llamado **payment.php**.

```php
<?PHP

// Set show errors for development, remove on production
error_reporting(E_ALL);
ini_set('display_errors', 1);


// Product dummy data
$product_data = array(
    "price" => 5.99,
    "invoice" => "I-TS00005",
    "description" => "A nice T-shirt"
);


/**
 *  Manage Return and Cancel
 */

// If requesting the Return page
if (isset($_GET['return'])) {
    exit("<h3>Thank you!!!</h3>");
}

// If requesting the Cancel page
if (isset($_GET['cancel'])) {
    exit("<h3>Operation Cancelled. <br> Click the Pay with Credit Card button to try again</h3>");
}

/**
 *  Build XML
 */

// Create XML Skeleton string with defaults
$xmlStr = <<<XML
<getHostedPaymentPageRequest xmlns="AnetApi/xml/v1/schema/AnetApiSchema.xsd">
    <merchantAuthentication>
    </merchantAuthentication>
    <transactionRequest>
        <transactionType>authCaptureTransaction</transactionType>
        <amount></amount>
        <order></order>
    </transactionRequest>
    <hostedPaymentSettings>
        <setting>
            <settingName>hostedPaymentReturnOptions</settingName>
        </setting>
        <setting>
            <settingName>hostedPaymentPaymentOptions</settingName>
            <settingValue>{"cardCodeRequired": true, "showCreditCard": true, "showBankAccount": false}</settingValue>
        </setting>
        <setting>
            <settingName>hostedPaymentButtonOptions</settingName>
            <settingValue>{"text": "Pay Now"}</settingValue>
        </setting>
        <setting>
            <settingName>hostedPaymentBillingAddressOptions</settingName>
            <settingValue>{"show": false, "required":false}</settingValue>
        </setting>
    </hostedPaymentSettings>
 </getHostedPaymentPageRequest>
XML;

// Create XML object with string.
$xml = simplexml_load_string($xmlStr, 'SimpleXMLElement', LIBXML_NOWARNING);

// Add API Credentials to XML
$xml->merchantAuthentication->addChild('name', getenv('API_LOGIN_ID'));
$xml->merchantAuthentication->addChild('transactionKey', getenv('TRANSACTION_KEY'));

// Add product details
$xml->transactionRequest->amount = $product_data['price'];
$xml->transactionRequest->order->addChild('invoiceNumber', $product_data['invoice']);
$xml->transactionRequest->order->addChild('description', $product_data['description']);

// Add return options
$retUrl = json_encode(array("showReceipt" => false, 'url' => thisPageURL() . "?return", "urlText" => "Continue", "cancelUrl" => thisPageURL() . "?cancel", "cancelUrlText" => "Cancel"), JSON_UNESCAPED_SLASHES);
$xml->hostedPaymentSettings->setting[0]->addChild('settingValue', $retUrl);

// uncomment to see final XML
//echo "<pre>";
//print_r($xml);
//echo "</pre>";


function thisPageURL()
{
    $pageURL = 'http';
    if ($_SERVER["HTTPS"] == "on") {
        $pageURL .= "s";
    }
    $pageURL .= "://";
    if ($_SERVER["SERVER_PORT"] != "80") {
        $pageURL .= $_SERVER["SERVER_NAME"] . ":" . $_SERVER["SERVER_PORT"] . $_SERVER["REQUEST_URI"];
    } else {
        $pageURL .= $_SERVER["SERVER_NAME"] . $_SERVER["REQUEST_URI"];
    }
    $pageLocation = str_replace('index.php', '', $pageURL);
    return $pageLocation;
}



/**
 * Now Request the Token
 */
$url = "https://apitest.authorize.net/xml/v1/request.api"; // For Tests
//$url = "https://api.authorize.net/xml/v1/request.api"; // For live

try {
    // Setting curl parameters.
    $ch = curl_init();
    if (false === $ch) {
        throw new Exception('failed to initialize');
    }

    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/xml'));
    curl_setopt($ch, CURLOPT_POSTFIELDS, $xml->asXML());
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 300);
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false); //for production, set value to true or 1
    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false); //for production, set value to 2
    curl_setopt($ch, CURLOPT_DNS_USE_GLOBAL_CACHE, false);

    $content = curl_exec($ch);
    $content = str_replace('xmlns="AnetApi/xml/v1/schema/AnetApiSchema.xsd"', '', $content);

    if (false === $content) {
        throw new Exception(curl_error($ch), curl_errno($ch));
    }

    $hostedPaymentResponse = new SimpleXMLElement($content);
    curl_close($ch);
} catch (Exception $e) {
    trigger_error(sprintf('Curl failed with error #%d: %s', $e->getCode(), $e->getMessage()), E_USER_ERROR);
}

// If error, show. Remove this in production
if ($hostedPaymentResponse->messages->resultCode == "Error") {
    echo "<pre>";
    print_r($hostedPaymentResponse);
    echo "</pre>";
}

?>
```

### 5. Solicitar la Forma

El código mostrado abajo se debe insertar en el mismo archivo **payment.php**, justo debajo del código anterior.

Si se abre el archivo payment.php en un navegador, se verá un botón con el texto **Pay With Credit Card**, esta no es la forma de pago, sino apenas la solicitud de la forma final de pago, esta forma de solicitud deberá contar con un campo oculto con nombre **token** y el valor de este campo sera el string del token obtenido en el paso anterior, el cual se ha guardado en `$hostedPaymentResponse->token`.
Cuando se hace el envío de esta forma, se esta solicitando la forma de pago, y gracias al script **accept.js** se insertará en el iframe definido en el parámetro `target`. Si no se especifica un target, se abrirá la forma de pago en una ventana separada.

Si en la aplicación no se desea el paso de presionar el botón para obtener la forma, se puede hacer el submit con Javascript inmediatamente después de generarla, se adiciona el script para esta tarea al final.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Payment</title>
    <script src="https://jstest.authorize.net/v1/Accept.js"></script>
  </head>

  <body>
    <!-- action="https://test.authorize.net/payment/payment" // Production -->
    <form
      target="payframe"
      id="send_hptoken"
      action="https://test.authorize.net/payment/payment"
      method="post"
    >
      <input
        type="hidden"
        name="token"
        id="token"
        value="<?php echo $hostedPaymentResponse->token; ?>"
      />
      <input type="submit" value="Pay With Credit Card" />
    </form>
    <iframe
      style="height:700px;width:100%;border-style: none;"
      name="payframe"
      id="payframe"
    ></iframe>

    <script>
      document.addEventListener("DOMContentLoaded", function() {
        document.getElementById("send_hptoken").submit();
      });
    </script>
  </body>
</html>
```

### 6. Despliegue y prueba.

Si todo salio bien hasta el momento, se debe desplegar la forma de pago dentro del iFrame. Se muestra a continuación con datos de prueba de tarjeta visa.

![Authorize.net Hosted Payment Form](/images/authorize-hosted-form-cc.png)

Después de enviar el pago con el botón `Pay Now` se obtendrá la respuesta y el botón de continuar, el cual enviara a la página establecida como `url` dentro del `authCaptureTransaction` del XML. En nuestro caso pusimos la misma pagina con un parámetro, el cual leemos al inicio para desplegar un mensaje y abortar el resto del código.

![Authorize.net Hosted Payment Receipt](/images/authorize-hosted-form-receipt.png)

Por último, Authorize.net enviara la notificación del pago por correo.

## Conclusiones

Este método es el mas sencillo de implementar y en este post se ha mostrado con un uso mínimo. Obviamente se pueden aumentar las funcionalidades, por ejemplo el XML de solicitud de forma de pago se puede personalizar para mostrar la forma con mas datos como la dirección de facturación y otras funcionalidades. Y utilizando opciones un poco mas avanzadas, se puede mostrar la forma en una ventana emergente, etc. Inclusive adicionar soporte para identificación de clientes, donde la forma podrá contener lista de métodos de pago previamente almacenados. Mas información en la página de [Authorize Hosted](https://developer.authorize.net/api/reference/features/accept_hosted.html).
