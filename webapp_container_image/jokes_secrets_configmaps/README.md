

    ```bash
    kubectl run jokes --image=nginxinc/nginx-unprivileged:stable-alpine
    ```

    ```bash
    kubectl create configmap cm-jokes --from-file=./index.html
    ```


    ```bash
    kubectl create secret generic secret-jokes --from-literal=SPECIAL_MESSAGE="You found the hidden punchline! 🎯"
    ```

