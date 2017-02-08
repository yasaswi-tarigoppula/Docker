

    The CMD strings will be appended to the ENTRYPOINT in order to generate the container's command string.

    docker run --rm -d -p 80:80 --name test_nginx vinodhbasavani/nginx:4.0 "daemon off;"


    since /usr/sbin/nginx -g is added in the ENTRYPOINT instruction.
