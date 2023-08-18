Collecting user information through backdoor code auditing

## Version V7.1.0

File install/index.php

Positions 47 rows -102 rows, 136 rows -217 rows, 594 rows -606 rows

Instructions for installing through curl_ The post function sends data such as user website address, email address, installation time, software type, website name, keywords, description, program version, PHP version, database version, browser information, agent information, etc. to the server pointed to by u. mituo. cn

### code 1
~~~
public function doInstall()
{
    $action = $_GET['action'];
    if ($action == 'guide') {
        $string = @file_get_contents('https://u.mituo.cn/api/metinfo/license');
        file_put_contents('../upload/file/license.html', $string);
        if (!class_exists('SQLite3')) {
            header('location:index.php?action=inspect');
            die;
        }
    }
    switch ($action) {
        case 'apitest':
            $post = array('domain' => $_SERVER['HTTP_HOST']);
            $res = self::curl_post($post, 15);
            if (isset($res['status'])) {
                echo 'ok';
                die;
            } else {
                echo 'nohost';
                die;
            }
            break;
        case 'skipInstall':

        if (!class_exists('SQLite3')) {
            header('location:index.php?action=inspect');
            die;
        }
        $data = array(
            'version' => '7.0.0',
            'db_type' => 'sqlite',
            'info' => json_encode(array('php_ver' => PHP_VERSION)),
        );
        self::curl_post($data, 15);

        $fp = @fopen('../config/install.lock', 'w');
        @fwrite($fp, ' ');
        @fclose($fp);
        @chmod('../config/install.lock', 0554);

        $db = array('db_type' => 'sqlite', 'tablepre' => 'met_');
        define('PATH_CONFIG', '../config/');
        setDbConfig($db);
        header('location:../index.php');
            break;
        default:

            $_SESSION['install'] = 'metinfo';
            $m_now_time = time();
            $m_now_date = date('Y-m-d H:i:s', $m_now_time);
            $nowyear = date('Y', $m_now_time);
            include $this->template('index');
            break;
    }
}
~~~

### code 2
~~~
private function curl_post($post, $timeout)
{
    global $met_weburl, $met_host, $met_file;
    $post['referer'] = $this->geturl();
    $post['user_agent'] = $_SERVER['HTTP_USER_AGENT'];
    $host = 'u.mituo.cn';
    $file = '/api/metinfo/install';
    if (get_extension_funcs('curl') && function_exists('curl_init') && function_exists('curl_setopt') && function_exists('curl_exec') && function_exists('curl_close')) {
        $curlHandle = curl_init();
        curl_setopt($curlHandle, CURLOPT_URL, 'https://'.$host.$file);
        curl_setopt($curlHandle, CURLOPT_REFERER, $met_weburl);
        curl_setopt($curlHandle, CURLOPT_RETURNTRANSFER, 1);
        curl_setopt($curlHandle, CURLOPT_CONNECTTIMEOUT, $timeout);
        curl_setopt($curlHandle, CURLOPT_TIMEOUT, $timeout);
        curl_setopt($curlHandle, CURLOPT_POST, 1);
        curl_setopt($curlHandle, CURLOPT_POSTFIELDS, $post);
        curl_setopt($curlHandle, CURLOPT_SSL_VERIFYPEER, false);
        curl_setopt($curlHandle, CURLOPT_SSL_VERIFYHOST, false);
        $result = curl_exec($curlHandle);
        curl_close($curlHandle);
    } else {
        if (function_exists('fsockopen') || function_exists('pfsockopen')) {
            $post_data = $post;
            $post = '';
            @ini_set('default_socket_timeout', $timeout);
            while (list($k, $v) = each($post_data)) {
                $post .= rawurlencode($k).'='.rawurlencode($v).'&';
            }
            $post = substr($post, 0, -1);
            $len = strlen($post);
            if (function_exists(fsockopen)) {
                $fp = @fsockopen($host, 80, $errno, $errstr, $timeout);
            } else {
                $fp = @pfsockopen($host, 80, $errno, $errstr, $timeout);
            }
            if (!$fp) {
                $result = '';
            } else {
                $result = '';
                $out = "POST $file HTTP/1.0\r\n";
                $out .= "Host: $host\r\n";
                $out .= "Referer: $met_weburl\r\n";
                $out .= "Content-type: application/x-www-form-urlencoded\r\n";
                $out .= "Connection: Close\r\n";
                $out .= "Content-Length: $len\r\n";
                $out .= "\r\n";
                $out .= $post."\r\n";
                fwrite($fp, $out);
                $inheader = 1;
                while (!feof($fp)) {
                    $line = fgets($fp, 1024);
                    if ($inheader == 0) {
                        $result .= $line;
                    }
                    if ($inheader && ($line == "\n" || $line == "\r\n")) {
                        $inheader = 0;
                    }
                }

                while (!feof($fp)) {
                    $result .= fgets($fp, 1024);
                }
                fclose($fp);
                str_replace($out, '', $result);
            }
        } else {
            $result = '';
        }
    }
    $result = trim($result);
    $res = json_decode($result, true);

    return $res;
}
~~~

### exc code 3
~~~
$data = array();
$data['info'] = json_encode(array(
    'email' => $email,
    'webname' => $webname,
    'keywords' => $webkeywords,
    'tel' => $tel,
    'php_ver' => PHP_VERSION,
    'mysql_ver' => mysqli_get_server_info($link),
));
$data['db_type'] = 'mysql';
$data['version'] = $ver['value'];

self::curl_post($data, 20);
~~~
