---
title: '备份VPS内容到dropbox'
date: 2012-04-14 09:16:45
author: linpx
categories: octopress computer
comments: true
layout: post
markup: markdown
published: true
---
我对github一筹莫展，昨晚更新了博客内容后，还是发现无法在github上备份，除非我用心去学这东西，不然问题还是会一再出现。于是就自然想到了用dropbox来备份我的vps和博客内容。

首先被否定了的是dropbox官网上贴出的[linux安装教程](https://www.dropbox.com/install?os=lnx)。
运行出错，考虑到机子内存本身不大，于是就掠过了。

继续在网上晃荡，直到我看见了这篇[Backup your Linux system to Dropbox](
http://davehope.co.uk/Blog/backup-your-linux-vps-to-dropbox/)。原
理是利用mysql登录dropbox，将文件打包上传，由于vps带宽的原因，我发现几乎是妙传。贴出code，供大伙参 考。<!--more-->
{% codeblock %}
#!/bin/bash
DROPBOX_USER="Your Dropbox username"
DROPBOX_PASS="Your Dropbox password"
DROPBOX_DIR="Directory in your dropbox account to store the backups, e.g.
/backups"
BACKUP_SRC="/home /var/www /var/git /etc /root"
BACKUP_DST="/tmp"
MYSQL_SERVER="127.0.0.1"
MYSQL_USER="root"
MYSQL_PASS="Your MySQL password"

#
# Stop editing here.
NOW=$(date +"%Y.%m.%d")
DESTFILE="$BACKUP_DST/$NOW.tgz"

#
# Upload a file to Dropbox.
# $1 = Source file
# $2 = Destination file.
function dropboxUpload
{
    #
    # Code based on DropBox Uploader 0.6 from
http://www.andreafabrizi.it/?dropbox_uploader
    LOGIN_URL="https://www.dropbox.com/login"
    HOME_URL="https://www.dropbox.com/home"
    UPLOAD_URL="https://dl-web.dropbox.com/upload"
    COOKIE_FILE="/tmp/du_cookie_$RANDOM"
    RESPONSE_FILE="/tmp/du_resp_$RANDOM"

    UPLOAD_FILE=$1
    DEST_FOLDER=$2

    # Login
    echo -ne " > Logging in..."
    curl -s -i -c $COOKIE_FILE -o $RESPONSE_FILE --data
"login_email=$DROPBOX_USER&login_password=$DROPBOX_PASS&t=$TOKEN"
"$LOGIN_URL"
    grep "location: /home" $RESPONSE_FILE > /dev/null

    if [ $? -ne 0 ]; then
        echo -e " Failed!"
        rm -f "$COOKIE_FILE" "$RESPONSE_FILE"
        exit 1
    else
        echo -e " OK"
    fi

    # Load home page
    echo -ne " > Loading Home..."
    curl -s -i -b "$COOKIE_FILE" -o "$RESPONSE_FILE" "$HOME_URL"

    if [ $? -ne 0 ]; then
        echo -e " Failed!"
        rm -f "$COOKIE_FILE" "$RESPONSE_FILE"
        exit 1
    else
        echo -e " OK"
    fi

    # Get token
    TOKEN=$(cat "$RESPONSE_FILE" | tr -d '\n' | sed 's/.*<form
action="https:\/\/dl-web.dropbox.com\/upload"[^>]*>\s*<input type="hidden"
name="t" value="\([a-z 0-9]*\)".*/\1/')

    # Upload file
    echo -ne " > Uploading '$UPLOAD_FILE' to 'DROPBOX$DEST_FOLDER/'..."
    curl -s -i -b $COOKIE_FILE -o $RESPONSE_FILE -F "plain=yes" -F
"dest=$DEST_FOLDER" -F "t=$TOKEN" -F "file=@$UPLOAD_FILE"  "$UPLOAD_URL"
    grep "HTTP/1.1 302 FOUND" "$RESPONSE_FILE" > /dev/null

    if [ $? -ne 0 ]; then
        echo -e " Failed!"
        rm -f "$COOKIE_FILE" "$RESPONSE_FILE"
        exit 1
    else
        echo -e " OK"
        rm -f "$COOKIE_FILE" "$RESPONSE_FILE"
    fi
}

# Backup files.
mysqldump -u $MYSQL_USER -h $MYSQL_SERVER -p$MYSQL_PASS --all-databases >
"$NOW-Databases.sql"
tar cfz "$DESTFILE" $BACKUP_SRC "$NOW-Databases.sql"

dropboxUpload "$DESTFILE" "$DROPBOX_DIR"

rm -f "$NOW-Databases.sql" "$DESTFILE"


{% endcodeblock %}

完了大伙可以把这个加到crontab去，设定一个更新的循环时间就行。我的情况是，直接加在我邮件发送博客的批处理命令后，一旦邮件更新后，便自动打包上传了。很方便。
