## Version metinfo V7.8
File install/index.php
Position 81 rows -103 rows
Referencing : a function curl use for send to api url
~~~
public function curl_post($post, $timeout = 20)
    {
        $post['referer'] = $referer = $this->geturl(); //default to send url
        $post['user_agent'] = $_SERVER['HTTP_USER_AGENT'];  //default to send agent information
        $api = 'https://u.mituo.cn/api/metinfo/install';  //this is get information API url
        $result = '';

        if (get_extension_funcs('curl') && function_exists('curl_init') && function_exists('curl_setopt') && function_exists('curl_exec') && function_exists('curl_close')) {
            $curlHandle = curl_init();
            curl_setopt($curlHandle, CURLOPT_URL, $api);
            curl_setopt($curlHandle, CURLOPT_REFERER, $referer);
            curl_setopt($curlHandle, CURLOPT_RETURNTRANSFER, 1);
            curl_setopt($curlHandle, CURLOPT_CONNECTTIMEOUT, $timeout);
            curl_setopt($curlHandle, CURLOPT_TIMEOUT, $timeout);
            curl_setopt($curlHandle, CURLOPT_POST, 1);
            curl_setopt($curlHandle, CURLOPT_POSTFIELDS, $post);
            curl_setopt($curlHandle, CURLOPT_SSL_VERIFYPEER, false);
            curl_setopt($curlHandle, CURLOPT_SSL_VERIFYHOST, false);
            $result = curl_exec($curlHandle);
            curl_close($curlHandle);
        }
        return json_decode(trim($result), true);;
    }
~~~

## Version metinfo V7.8
File install/install_dmsql.php
Position 373-381 rows

File install/install_mysql.php
Position 368-377 rows

Referencing in the target code section when modifying website settings in the background https://u.mituo.cn/api/metinfo/install
Send url, agent , webname, keywords, version, sql_version to the server pointed to by api.metinfo.cn through concatenation query parameters
~~~
$data = array();
$data['info'] = json_encode(array(
    'webname' => $webname,
    'keywords' => $webkeywords,
    'php_ver' => PHP_VERSION,
    'mysql_ver' => mysqli_get_server_info($link),
));
$data['db_type'] = 'mysql';
$data['version'] = $this->sys_ver;
self::curl_post($data, 20);  //excute function
~~~
