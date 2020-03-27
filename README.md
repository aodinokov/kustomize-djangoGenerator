# kustomize-jinjaPlugin

PoC Kustomize generator and transformer (all-in-one) plugin to show the power of Jinja

Note: base dir contains encrypted data. to run the example please use the following command:

    JINJA_CRYPT_PASSWORD="multipass" JINJA_DEBUG=0 XDG_CONFIG_HOME=$(pwd) kustomize build --enable_alpha_plugins overlays/y

