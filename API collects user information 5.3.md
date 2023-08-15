# aboutMetinfo
Metinfo Code Analysis 1：API collects user information 5.3

## Version V5.3
File install/index.php
Position 472 lines -483 lines
Referencing in the target code section during installation http://api.metinfo.cn/record_install.php Send user URL, email, installation time, software type, website name, keywords, description, program version, PHP version, database version, browser information, agent information, and other data to the server pointed to by api.metinfo.cn through concatenation and query parameters
~~~pre
$query="select * from $met_config where name='metcms_v'";
$ver=$db->get_one($query);
$webname=$webname_cn?$webname_cn:($webname_en?$webname_en:'');
$webkeywords=$webkeywords_cn?$webkeywords_cn:($webkeywords_en?$webkeywords_en:'');
$spt = '<script type="text/javascript" src="http://api.metinfo.cn/record_install.php?';
$spt .= "url=" .$install_url;
$spt .= "&email=".$email."&installtime=".$m_now_date."&softtype=1";
$spt .= "&webname=".$webname."&webkeywords=".$webkeywords."&tel=".$tel;
$spt .= "&version=".$ver[value]."&php_ver=" .PHP_VERSION. "&mysql_ver=" .mysql_get_server_info()."&browser=".$_SERVER['HTTP_USER_AGENT'].'|'.$se360;
$spt .= "&agents=".$agents;
$spt .= '"></script>';
echo $spt;
~~~

## Version V5.3
File app/system/admin/webset/webset.class.php
Position 20 rows -31 rows
Referencing in the target code section when modifying website settings in the background http://api.metinfo.cn/record_install.php Send user website backend management path, administrator contact email, administrator contact phone number, website address, email address, installation time, software type, website name, keywords, description, program version, PHP version, database version, browser information, agent information, and other data to the server pointed to by api.metinfo.cn through concatenation query parameters
~~~pre
function doindex() {
    global $_M;
    nav::select_nav(1); 
    $record = '';
    if($_M['form']['turnovertext']){
        $adrry = admin_information();
        $email = $adrry['admin_email'];
        $tel   = $adrry['admin_mobile'];
        $record = "http://api.metinfo.cn/record_install.php?url={$_M['config']['met_weburl']}&email={$email}&webname={$_M['config']['met_webname']}&webkeywords={$_M['config']['met_keywords']}&tel={$tel}&version={$_M['config']['metcms_v']}&softtype=1";
    }
    require $this->template('tem/index');
}
~~~




