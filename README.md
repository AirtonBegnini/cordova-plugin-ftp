# cordova-plugin-ftp

## Description

This cordova plugin is created to use ftp (client) in web/js.

Support both **iOS** and **Android** platform now.

You can do the following:

- List a directory
- Create a directory
- Delete a directory (must be empty)
- Delete a file
- Download a file (with percent info)
- Upload a file (with percent info)
- Cancel upload/download

## Installation

```sh
$ cordova plugin add https://github.com/xfally/cordova-plugin-ftp
$ cordova prepare
```

> It has not been added to npm registry yet.

## Usage

You can access this plugin by js object `window.cordova.plugin.ftp`.

**DEMO**

```js
// test code (for angularjs)
// Tip: Usually init/create $window.cordova.plugin.ftp will take some time, so set a timeout() to make sure it's ready.
$timeout(function() {
	if ($window.cordova.plugin.ftp) {
		$log.log("xtest: ftp: found");
		// 1. connect to one ftp server, then you can do any actions/cmds
		$window.cordova.plugin.ftp.connect("192.168.1.1", "anonymous", "anonymous@", function() {
			$log.log("xtest: ftp: connect ok");
			// 2. list one dir, note that just can be dir, not file
			$window.cordova.plugin.ftp.ls("/sdcard", function(fileList) {
				$log.log("xtest: ftp: list ok");
				if (fileList && fileList.length > 0) {
					$log.log("xtest: ftp: The last file'name is " + fileList[fileList.length - 1].name);
					$log.log("xtest: ftp: The last file'type is " + fileList[fileList.length - 1].type);
					$log.log("xtest: ftp: The last file'link is " + fileList[fileList.length - 1].link);
					$log.log("xtest: ftp: The last file'size is " + fileList[fileList.length - 1].size);
					// 3. create one dir on ftp server
					$window.cordova.plugin.ftp.mkdir("/sdcard/mkdir", function(ok) {
						$log.log("xtest: ftp: mkdir ok=" + ok);
						// 4. upload local file to remote, you can rename at the same time. arg1: local file, arg2: remote file.
						// make sure you can access and read the local file.
						$window.cordova.plugin.ftp.upload("/default.prop", "/sdcard/mkdir/default.prop", function(percent) {
							if (percent == 1) {
								$log.log("xtest: ftp: upload finish");
								// cancel download after some time
								//$timeout(function() {
									//$window.cordova.plugin.ftp.cancel(function(ok) {
										//$log.log("xtest: ftp: cancel ok=" + ok);
									//}, function(error) {
										//$log.log("xtest: ftp: cancel error=" + error);
									//});
								//}, 2000);
								// 5. download remote file to local, you can rename at the same time. arg1: local file, arg2: remote file.
								// make sure you can access and write the local dir.
								$window.cordova.plugin.ftp.download("/mnt/sdcard/download.mp4", "/sdcard/视频/mp4-10MB-720P.mp4", function(percent) {
									if (percent == 1) {
										$log.log("xtest: ftp: download finish");
										// 6. delete one file on ftp server
										$window.cordova.plugin.ftp.rm("/sdcard/mkdir/default.prop", function(ok) {
											$log.log("xtest: ftp: rm ok=" + ok);
											// 7. delete one dir on ftp server, note that just can be empty dir, or will fail
											$window.cordova.plugin.ftp.rmdir("/sdcard/mkdir", function(ok) {
												$log.log("xtest: ftp: rmdir ok=" + ok);
											}, function(error) {
												$log.log("xtest: ftp: rmdir error=" + error);
											});
										}, function(error) {
											$log.log("xtest: ftp: rm error=" + error);
										});
									} else {
										$log.log("xtest: ftp: download percent=" + percent*100 + "%");
									}
								}, function(error) {
									$log.log("xtest: ftp: download error=" + error);
								});
							} else {
								$log.log("xtest: ftp: upload percent=" + percent*100 + "%");
							}
						}, function(error) {
							$log.log("xtest: ftp: upload error=" + error);
						});
					}, function(error) {
						$log.log("xtest: ftp: mkdir error=" + error);
					});
				}
			}, function(error) {
				$log.log("xtest: ftp: list error: " + error);
			});
		});
	} else {
		$log.log("xtest: ftp: not found!");
	}
}, 2000);
```

Refer to [ftp.js](https://github.com/xfally/cordova-plugin-ftp/blob/master/www/ftp.js) for more js API info.

## Notice

For iOS, `ftp.connect` will always success (even if `username` and `password` are incorrect), but it does NOT mean the later actions, e.g. `ls`... `download` will success too! So check their `errorCallback` carefully.

## Thanks

- The iOS native implementing is based on [GoldRaccoon](https://github.com/albertodebortoli/GoldRaccoon).
- The Android native implementing is based on [ftp4j](http://www.sauronsoftware.it/projects/ftp4j/) jar (LGPL).

