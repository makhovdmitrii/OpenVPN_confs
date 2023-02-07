# easy-rsa keys generation
cd /etc/openvpn
mkdir easy-rsa

cp -R /usr/share/easy-rsa/* easy-rsa/

#Edit /etc/openvpn/easy-rsa/vars bottom according to your organization.
#    export KEY_COUNTRY="US"
#    export KEY_PROVINCE="CA"
#    export KEY_CITY="SanFrancisco"
#    export KEY_ORG="Fort-Funston"
#    export KEY_EMAIL="mail@domain"
#    export KEY_EMAIL=mail@domain

cd easy-rsa/
touch keys/index.txt
echo 01 > keys/serial
. ./vars  # set environment variables
./clean-all

#Remember:
#- only .key files should be kept confidential.
#- .crt and .csr files can be sent over insecure channels such as plaintext email.
#- do not need to copy a .key file between computers.
#- each computer will have its own certificate/key pair.

source ./vars; ./build-ca
source ./vars; ./build-key-server server
source ./vars; ./build-dh
source ./vars; ./build-key user1
source ./vars; ./build-key user2
source ./vars; ./build-key user3
openssl dhparam -out dh1024.pem 1024

#Generate key with password (this protect the key and request the password every time that you connect to the server), for each client:
#./build-key-pass clientname
#It will generate keys in /etc/openvpn/easy-rsa/keys/
#Copy the ca.crt, clientname.crt, clientname.key from Server to Client /etc/openvpn/easy-rsa/keys/ directory.

#New easyrsa version
cd /etc/openvpn
mkdir easy-rsa

cp -R /usr/share/easy-rsa/* easy-rsa/

cp vars.example vars
# Edit vars setup at the end add this:
#    export KEY_COUNTRY="US"
#    export KEY_PROVINCE="CA"
#    export KEY_CITY="SanFrancisco"
#    export KEY_ORG="Fort-Funston"
#    export KEY_EMAIL="mail@domain"

./easyrsa build-ca nopass
./easyrsa build-server-full server nopass
./easyrsa gen-dh nopass
./easyrsa build-client-full user1 nopass
./easyrsa build-client-full user2 nopass
./easyrsa build-client-full user3 nopass
./easyrsa build-client-full user4 nopass
./easyrsa build-client-full user5 nopass
openssl dhparam -out dh1024.pem 1024
