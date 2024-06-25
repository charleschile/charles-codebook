
逆天mo_ctl的下载设置

mac中需要使用curl并需要将install.sh中的wget相关代码改成如下：
```bash
for site in ${SITES[@]}; do
    URL="${site}/mo_ctl_standalone/archive/refs/heads/main.zip"
    add_log "I" "Try to download mo_ctl from URL: ${URL}"
    if curl -L --retry 2 --retry-delay 30 --max-time 60 -o ${DOWNLOAD_FILE_RENAME} ${URL}; then
        add_log "I" "Successfully downloaded mo_ctl"
        rc="0"
        break
    fi
done

```



Arjun, I have deployed MO on my mac and have tried the connection, writing, export, and read data functions of the MO. 