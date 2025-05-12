# 3x-ui forward host


## index.php file

```
<?php

$logFile = '/home/username/public_html/sub/error.log';


$panelAddress = "https:/subdomain.com:443/sub/";    //subdomain.com is your domain name   


$randomString = isset($_SERVER['QUERY_STRING']) ? $_SERVER['QUERY_STRING'] : '';

if (empty($randomString) || $randomString === '/') {
    $randomString = bin2hex(random_int(100000, 999999));
    header("Location: /sub/$randomString");
    exit();
}

$fullPanelAddress = $panelAddress . $randomString;

$ch = curl_init($fullPanelAddress);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_FOLLOWLOCATION, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
$response = curl_exec($ch);

if ($response === false) {
    $error = curl_error($ch);
}
$httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
curl_close($ch);

if ($httpCode == 200) {
    header('Content-Type: text/plain');
    echo $response;
} else {
    http_response_code($httpCode);
    echo "Error: Unable to fetch subscription data. HTTP Code: $httpCode";
}
exit();
?>

```


## .htaccess

```
RewriteEngine On
RewriteBase /
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^sub66/([a-zA-Z0-9]+)$ sub/index.php?$1 [L,QSA]
RewriteRule ^sub/([a-zA-Z0-9]+)$ sub/index.php?$1 [L,QSA]
```
