#!/usr/bin/env php
<?php
/*
  Copyright 2011 - Jonas Genannt <jonas@brachium-system.net>

  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
*/
ini_set('memory_limit', '100M');
if($argc != 2) {
        print "usage: check_wordpress <path to wordpress>\n";
        exit(1);
}
$update_core    = array();
$update_plugins = array();
$update_themes  = array();

chdir($argv[1]);
require_once('./wp-load.php');
global $wp_version;

wp_update_plugins();
wp_version_check();
wp_update_themes();

$core = get_site_transient('update_core');
$plugins = get_site_transient('update_plugins');
$themes = get_site_transient('update_themes');

if ($themes) {
	foreach($themes->response as $theme) {
		array_push($update_themes, $theme['theme']);
	}
}


if ($plugins) {
	foreach($plugins->response as $plugin) {
		array_push($update_plugins, $plugin->slug);
	}
}
if ($core) {
	$arr_core = $core->updates;
	if (is_array($arr_core) && $arr_core[0]) {
		$obj_core = $arr_core[0];
		if ($obj_core->response && $obj_core->response != "latest") {
			$update_core = $obj_core->current . " [" . $obj_core->locale . "]";
		}
	}
}

/*
print "Installed WP Version: $wp_version\n";
print "Core: $update_core\n";
print "Plugins: " . join($update_plugins,',') . "\n";
print "Themes: " . join($update_themes,',') . "\n";
*/

if ( $update_core || $update_plugins || $update_themes) {
	$message = "Wordpress: ";
	$err_code = 0;
	if ($update_core) {
		$message .= "Core Upgrade ($wp_version -> $update_core)";
		$err_code = 2;
	}
	else {
		$message .= "Core OK";
	}

	$message .= " - ";

	if ($update_plugins) {
		$message .= "Plugins nedded (" . join($update_plugins, ",") . ")";
		if ($err_code == 0) $err_code = 1;
	}
	else {
		$message .= "Plugins OK";
	}

	$message .= " - ";
	if ($update_themes) {
		$message .= "Themes to upgrade (" . join($update_themes, ",") . ")";
		if ($err_code == 0) $err_code = 1;
	}
	else {
		$message .= "Themes OK";
	}
	echo "$message\n";
	exit($err_code);
}
else {
	if ($wp_version < 3 ) {
		echo "Wordpress Version < 3 - Check Script does not work - Upgrade ASAP!\n";
		exit(3);
	}
	echo "Wordpress: OK ($wp_version)\n";
	exit();
}

?>
