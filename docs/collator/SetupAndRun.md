- wyciągnij najnowszy pojemnik na kalmary
 #!/bin/bash

docker pull mantanetwork/calamari:latest

pakiet manta .rpm zawiera:

plik binarny manta (który jest również używany do uruchamiania kalmarów)
usługi systemowe manta i kalmary
specyfikacje łańcuszków manta, kalmary, polkadot i kusama
skrypt, który uruchamia się po instalacji i tworzy konto systemowe manta, pod którym działa usługa systemd
zacznij (zobacz też: rpm.manta.systems):

dodaj repozytorium manta .rpm
 
#!/bin/bash

sudo dnf install dnf-plugins-core
sudo dnf config-manager --add-repo https://rpm.manta.systems/manta.repo
sudo dnf config-manager --set-enabled manta
sudo dnf update

zainstaluj manta

#!/bin/bash

sudo dnf install manta

pakiet manta .deb zawiera:

plik binarny manta (który jest również używany do uruchamiania kalmarów)
usługi systemowe manta i kalmary
specyfikacje łańcuszków manta, kalmary, polkadot i kusama
skrypt, który uruchamia się po instalacji i tworzy konto systemowe manta, pod którym działa usługa systemd
zacznij (zobacz też:deb.manta.systems):

dodaj repozytorium manta .deb

#!/bin/bash

sudo curl -o /usr/share/keyrings/manta.gpg https://deb.manta.systems/manta.gpg
sudo curl -o /etc/apt/sources.list.d/manta.list https://deb.manta.systems/manta.list
sudo apt update

zainstaluj manta

#!/bin/bash

sudo apt install manta

pobierz plik binarny, specyfikacje łańcucha i plik jednostki systemd

#!/bin/bash

# intall jq on ubuntu
sudo apt install jq

# or on fedora
sudo dnf install jq

# get the latest version of binary
manta_version=$(curl -s https://api.github.com/repos/Manta-Network/Manta/releases/latest | jq -r .tag_name | cut -c 2-)

# binary
sudo curl -Lo /usr/local/bin/manta https://github.com/Manta-Network/Manta/releases/download/v${manta_version}/manta
sudo ln -srf /usr/local/bin/manta /usr/local/bin/calamari

# chainspecs
sudo mkdir -p /usr/share/substrate
sudo curl -Lo /usr/share/substrate/calamari.json https://raw.githubusercontent.com/Manta-Network/Manta/v3.0.9/genesis/calamari-genesis.json
sudo curl -Lo /usr/share/substrate/kusama.json https://raw.githubusercontent.com/paritytech/polkadot/master/node/service/res/kusama.json

# systemd unit file
sudo curl -Lo /etc/systemd/system/calamari.service https://raw.githubusercontent.com/Manta-Network/Manta/deb-rpm/scripts/package/calamari.service

utwórz konto systemowe manta, pod którym działa usługa systemd

#!/bin/bash

sudo groupadd --system manta
sudo useradd \
  --system \
  --gid manta \
  --home-dir /var/lib/substrate \
  --create-home \
  --shell /sbin/nologin \
  --comment 'service account for manta and calamari services' \
  manta

Konfiguracja
niektóre dodatkowe parametry wiersza poleceń są wymagane lub pomocne przy sortowaniu.

edytuj plik jednostki serwisowej kalmarów, aby uwzględnić parametry sortowania w poleceniu ExecStart.
/usr/lib/systemd/system/calamari.service

ExecStart=/usr/bin/calamari \
    --collator \
    --name 'my parachain collator node name' \
    --chain /usr/share/substrate/calamari.json \
    --base-path /var/lib/substrate \
    --port 31333 \
    --ws-port 9144 \
    --ws-max-connections 100 \
    --rpc-port 9133 \
    --rpc-cors all \
    --rpc-methods auto \
    --prometheus-port 9615 \
    --prometheus-external \
    --state-cache-size 0 \
    --bootnodes \
        /dns/crispy.calamari.systems/tcp/30333/p2p/12D3KooWNE4LBfkYB2B7D4r9vL54YMMGsfAsXdkhWfBw8VHJSEQc \
        /dns/crunchy.calamari.systems/tcp/30333/p2p/12D3KooWL3ELxcoMGA6han3wPQoym5DKbYHqkWkCuqyjaCXpyJTt \
        /dns/hotdog.calamari.systems/tcp/30333/p2p/12D3KooWBdto53HnArmLdtf2RXzNWti7hD5mML7DWGZPD8q4cywv \
        /dns/tasty.calamari.systems/tcp/30333/p2p/12D3KooWGs2hfnRQ3Y2eAoUyWKUL3g7Jmcsf8FpyhVYeNpXeBMSu \
        /dns/tender.calamari.systems/tcp/30333/p2p/12D3KooWNXZeUSEKRPsp1yiDH99qSVawQSWHqG4umPjgHsn1joci \
    -- \
    --name 'my embedded relay node name' \
    --chain /usr/share/substrate/kusama.json \
    --port 31334 \
    --ws-port 9145 \
    --rpc-port 9134 \
    --prometheus-port 9616 \
    --prometheus-external \
    --telemetry-url 'wss://api.telemetry.manta.systems/submit/ 0'

edytuj plik jednostki serwisowej kalmarów, aby uwzględnić parametry sortowania w poleceniu ExecStart.
/usr/lib/systemd/system/calamari.service

ExecStart=/usr/bin/calamari \
    --collator \
    --name 'my parachain collator node name' \
    --chain /usr/share/substrate/calamari.json \
    --base-path /var/lib/substrate \
    --port 31333 \
    --ws-port 9144 \
    --ws-max-connections 100 \
    --rpc-port 9133 \
    --rpc-cors all \
    --rpc-methods auto \
    --prometheus-port 9615 \
    --prometheus-external \
    --state-cache-size 0 \
    --bootnodes \
        /dns/crispy.calamari.systems/tcp/30333/p2p/12D3KooWNE4LBfkYB2B7D4r9vL54YMMGsfAsXdkhWfBw8VHJSEQc \
        /dns/crunchy.calamari.systems/tcp/30333/p2p/12D3KooWL3ELxcoMGA6han3wPQoym5DKbYHqkWkCuqyjaCXpyJTt \
        /dns/hotdog.calamari.systems/tcp/30333/p2p/12D3KooWBdto53HnArmLdtf2RXzNWti7hD5mML7DWGZPD8q4cywv \
        /dns/tasty.calamari.systems/tcp/30333/p2p/12D3KooWGs2hfnRQ3Y2eAoUyWKUL3g7Jmcsf8FpyhVYeNpXeBMSu \
        /dns/tender.calamari.systems/tcp/30333/p2p/12D3KooWNXZeUSEKRPsp1yiDH99qSVawQSWHqG4umPjgHsn1joci \
    -- \
    --name 'my embedded relay node name' \
    --chain /usr/share/substrate/kusama.json \
    --port 31334 \
    --ws-port 9145 \
    --rpc-port 9134 \
    --prometheus-port 9616 \
    --prometheus-external \
    --telemetry-url 'wss://api.telemetry.manta.systems/submit/ 0'

edytuj plik jednostki serwisowej kalmarów, aby uwzględnić parametry sortowania w poleceniu ExecStart.
/etc/systemd/system/calamari.service

ExecStart=/usr/local/bin/calamari \
    --collator \
    --name 'my parachain collator node name' \
    --chain /usr/share/substrate/calamari.json \
    --base-path /var/lib/substrate \
    --port 31333 \
    --ws-port 9144 \
    --ws-max-connections 100 \
    --rpc-port 9133 \
    --rpc-cors all \
    --rpc-methods auto \
    --prometheus-port 9615 \
    --prometheus-external \
    --state-cache-size 0 \
    --bootnodes \
        /dns/crispy.calamari.systems/tcp/30333/p2p/12D3KooWNE4LBfkYB2B7D4r9vL54YMMGsfAsXdkhWfBw8VHJSEQc \
        /dns/crunchy.calamari.systems/tcp/30333/p2p/12D3KooWL3ELxcoMGA6han3wPQoym5DKbYHqkWkCuqyjaCXpyJTt \
        /dns/hotdog.calamari.systems/tcp/30333/p2p/12D3KooWBdto53HnArmLdtf2RXzNWti7hD5mML7DWGZPD8q4cywv \
        /dns/tasty.calamari.systems/tcp/30333/p2p/12D3KooWGs2hfnRQ3Y2eAoUyWKUL3g7Jmcsf8FpyhVYeNpXeBMSu \
        /dns/tender.calamari.systems/tcp/30333/p2p/12D3KooWNXZeUSEKRPsp1yiDH99qSVawQSWHqG4umPjgHsn1joci \
    -- \
    --name 'my embedded relay node name' \
    --chain /usr/share/substrate/kusama.json \
    --port 31334 \
    --ws-port 9145 \
    --rpc-port 9134 \
    --prometheus-port 9616 \
    --prometheus-external \
    --telemetry-url 'wss://api.telemetry.manta.systems/submit/ 0'

parametry o szczególnym znaczeniu dla opiekunów kolatorów
dwa zestawy parametrów są dostarczane do binarnego węzła substratu (kalmary), oddzielone podwójną kreską (--). pierwszy zestaw kontroluje zachowanie węzła parachain. drugi zestaw kontroluje zachowanie wbudowanego węzła łańcucha przekaźnikowego.

istotne parametry parachain
--collator: uruchom w trybie sortowania. zachowuje się tak samo jak --validator w łańcuchach przekaźników. ustawienie tego powoduje również ustawienie trybu przycinania na archiwum (jak --pruning archiwum).
--name: nazwa węzła parachain, wyświetlana w telemetrii kalmarów.
--port: parachainowy port peer-to-peer. Domyślnie kalmary to 31333. Ten port musi być dostępny przez Internet dla innych węzłów kalmarów.
--prometheus-port: port metryk parachain. domyślna wartość kalmarów to 9615. ten port musi być dostępny dla monitora manta metrics pod adresem: 18.156.192.254 (18.156.192.254/32, jeśli określasz według podsieci)
--prometheus-external: jeśli metryki odwrotnego proxy nie są używane przez protokół SSL, może być konieczne ustawienie tego parametru, aby nakazać wbudowanemu serwerowi metryk nasłuchiwanie na gnieździe all ips (0.0.0.0:9615), a nie tylko na hoście lokalnym (127.0). 0,1:9615)
istotne parametry łańcucha przekaźnikowego
--name: nazwa węzła łańcucha przekaźnikowego, wyświetlana w telemetrii kusama.
--port: port peer-to-peer łańcucha przekaźników. Domyślna wartość calamari-embedded-kusama to 31334. Ten port musi być dostępny przez Internet dla innych węzłów kusama.
--prometheus-port: port metryk łańcucha przekaźników. calamari-embedded-kusama domyślnie 9616. ten port musi być dostępny dla monitora manta metrics pod adresem: 18.156.192.254 (18.156.192.254/32, jeśli określasz według podsieci)
--prometheus-external: jeśli metryki odwrotnego proxy nie są używane przez protokół SSL, może być konieczne ustawienie tego parametru, aby nakazać wbudowanemu serwerowi metryk nasłuchiwanie na gnieździe all ips (0.0.0.0:9616), a nie tylko na hoście lokalnym (127.0). 0,1:9616)

wystawiać metryki węzłów do monitorowania
powinieneś monitorować swój własny sortownik, używając technik opisanych na wiki polkadot. metryki widoczne na portach 9615 i 9616 ułatwiają to, więc te porty (lub port 443, jeśli serwer proxy ssl) powinny być dostępne zarówno z własnego serwera prometheus/alertmanager (który należy skonfigurować tak, aby ostrzegał, używając alertmanager) i serwera pulsacyjnego manta o 18.156.192.254 (co jest monitorowane przez manta devops).

konfiguracja zapory
kilka portów musi być dostępnych spoza hosta węzła, aby sortownik działał prawidłowo. dla uproszczenia ustawienia opisane poniżej korzystają z portów domyślnych, jednak możesz swobodnie używać portów alternatywnych, zgodnie z wymaganiami Twojej infrastruktury i topologii sieci.

31333: domyślny port peer-to-peer kalmarów
31334: domyślny (embedded-relay) port peer-to-peer kusama
9615: domyślny port metryk kalmarów
9616: domyślny port metryk kusama (embedded-relay)
metryki odwrotnego proxy przez ssl z LetSencrypt i Nginx
dobrą praktyką jest serwowanie metryk powyżej:

ssl, aby można było zweryfikować ich autentyczność i pochodzenie
dns, dzięki czemu zmiany adresu IP nie wymagają aktualizacji serwera impulsowego
ułatwia to również czujnemu obserwatorowi ustalenie, które kolatory działają dobrze (lub słabo), gdy patrzą na nazwy domen, takie jak calamari.awesome-host.awesome-collators.com, w porównaniu z adresami IP i kombinacjami portów, takimi jak 123.123. 123.123:987, co może nie wskazywać na to, że obserwowany jest kolator i czy dana metryka odnosi się do łańcucha przekaźnikowego lub parachain.

łatwym sposobem na osiągnięcie tego jest zainstalowanie certbot i nginx oraz skonfigurowanie nasłuchiwania zwrotnego proxy na porcie 443, który przesyła żądania ssl proxy do lokalnych portów metryk.

poniższy przykład zakłada:

administrujesz domeną example.com
jego dns jest zarządzany przez cloudflare lub route53
nazwa hosta twoich węzłów to bob
Twój węzeł kalmarów używa domyślnych portów
port bramy internetowej (routera) przekazuje ruch 443/ssl przychodzący przez interfejs WAN routera do węzła sortującego
masz zainstalowanego certbota
::: uwaga poniżej przykłady cloudflare i route53. google python3-certbot-dns-${your_dns_provider} dla innych przykładów :::

zainstaluj certbota i wtyczkę do weryfikacji dns
#!/bin/bash

sudo dnf install \
  certbot \
  python3-certbot-dns-cloudflare \
  python3-certbot-dns-route53

#!/bin/bash

sudo apt-get install \
  certbot \
  python3-certbot-dns-cloudflare \
  python3-certbot-dns-route53

zażądać certyfikatu za pomocą wtyczki dns, aby certbot mógł automatycznie odnowić certyfikat w pobliżu daty wygaśnięcia. ręcznie żądane certyfikaty muszą być ręcznie aktualizowane, aby zachować ważność certyfikatów SSL, więc należy ich unikać.

#!/bin/bash

sudo certbot certonly \
  --dns-cloudflare \
  --dns-cloudflare-credentials .cloudflare-credentials \
  -d bob.example.com \
  -d calamari.metrics.bob.example.com \
  -d kusama.metrics.bob.example.com
#!/bin/bash

sudo certbot certonly \
  --dns-route53 \
  --dns-route53-propagation-seconds 30 \
  -d bob.example.com \
  -d calamari.metrics.bob.example.com \
  -d kusama.metrics.bob.example.com

skonfiguruj nginx /etc/nginx/sites-enabled/example.com.conf do odwrócenia subdomen DNS proxy na lokalne porty metryk.

server {
  server_name calamari.metrics.bob.example.com;
  listen 443 ssl;
  gzip off;
  location / {
    proxy_pass http://127.0.0.1:9615;
    proxy_http_version 1.1;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }
  ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
  include /etc/letsencrypt/options-ssl-nginx.conf;
  ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
}

server {
  server_name kusama.metrics.bob.example.com;
  listen 443 ssl;
  gzip off;
  location / {
    proxy_pass http://127.0.0.1:9616;
    proxy_http_version 1.1;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }
  ssl_certificate /etc/letsencrypt/live/bob.example.com/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/bob.example.com/privkey.pem;
  include /etc/letsencrypt/options-ssl-nginx.conf;
  ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
}

#!/bin/bash

default_zone=$(sudo firewall-cmd --get-default-zone)

# calamari p2p
sudo firewall-cmd \
  --zone=${default_zone} \
  --add-port=31333/tcp \
  --permanent

# kusama p2p
sudo firewall-cmd \
  --zone=${default_zone} \
  --add-port=31334/tcp \
  --permanent

# calamari metrics
sudo firewall-cmd \
  --zone=${default_zone} \
  --add-port=9615/tcp \
  --permanent

# kusama metrics
sudo firewall-cmd \
  --zone=${default_zone} \
  --add-port=9616/tcp \
  --permanent

sudo firewall-cmd --reload

Działanie uruchom swój węzeł dokera
docker run \
  -it \
  -p 9933:9933 \
  -p 30333:30333 \
  -v host_path:/container_path \
  --name your_container_name \
  mantanetwork/calamari:latest \
  --base-path /container_path/data \
  --keystore-path /container_path/keystore \
  --name your_collator_name \
  --rpc-cors all \
  --collator \
  --rpc-methods=unsafe \
  --unsafe-rpc-external

Przykłady tych nazw i ścieżek:
host_path:/container_path => ~/my-calamari-db:/calamari

your_collator_name => Community-Collator-1

Upewnij się, że widzisz taki wiersz dziennika:

sprawdź stan usługi kalmary:

#!/bin/bash

systemctl status calamari.service

włącz usługę kalmary (usługa uruchomi się automatycznie przy starcie systemu):
#!/bin/bash

sudo systemctl enable calamari.service

uruchom usługę kalmary:
#!/bin/bash

sudo systemctl start calamari.service

zatrzymaj usługę kalmarów:
#!/bin/bash

sudo systemctl stop calamari.service

ogon dzienników usług kalmarów:
#!/bin/bash

journalctl -u calamari.service -f

debuguj konfigurację usługi kalmarów (uruchom kalmary jako użytkownik manta, aby szybko sprawdzić błędy uruchomieniowe):
#!/bin/bash

sudo -H -u manta bash -c '/usr/bin/calamari --chain /usr/share/substrate/calamari.json --base-path /var/lib/substrate --port 31333 --ws-port 9144 --ws-max-connections 100 --rpc-port 9133 --rpc-cors all --rpc-methods safe --state-cache-size 0 --bootnodes /dns/crispy.calamari.systems/tcp/30333/p2p/12D3KooWNE4LBfkYB2B7D4r9vL54YMMGsfAsXdkhWfBw8VHJSEQc /dns/crunchy.calamari.systems/tcp/30333/p2p/12D3KooWL3ELxcoMGA6han3wPQoym5DKbYHqkWkCuqyjaCXpyJTt /dns/hotdog.calamari.systems/tcp/30333/p2p/12D3KooWBdto53HnArmLdtf2RXzNWti7hD5mML7DWGZPD8q4cywv /dns/tasty.calamari.systems/tcp/30333/p2p/12D3KooWGs2hfnRQ3Y2eAoUyWKUL3g7Jmcsf8FpyhVYeNpXeBMSu /dns/tender.calamari.systems/tcp/30333/p2p/12D3KooWNXZeUSEKRPsp1yiDH99qSVawQSWHqG4umPjgHsn1joci -- --chain /usr/share/substrate/kusama.json'

sprawdź stan usługi kalmary:
#!/bin/bash

systemctl status calamari.service

włącz usługę kalmary (usługa uruchomi się automatycznie przy starcie systemu):
#!/bin/bash

sudo systemctl enable calamari.service

uruchom usługę kalmary:
#!/bin/bash

sudo systemctl start calamari.service

zatrzymaj usługę kalmarów:
#!/bin/bash

sudo systemctl stop calamari.service

ogon dzienników usług kalmarów:
#!/bin/bash

journalctl -u calamari.service -f

debuguj konfigurację usługi kalmarów (uruchom kalmary jako użytkownik manta, aby szybko sprawdzić błędy uruchomieniowe):
#!/bin/bash

sudo -H -u manta bash -c '/usr/bin/calamari --chain /usr/share/substrate/calamari.json --base-path /var/lib/substrate --port 31333 --ws-port 9144 --ws-max-connections 100 --rpc-port 9133 --rpc-cors all --rpc-methods safe --state-cache-size 0 --bootnodes /dns/crispy.calamari.systems/tcp/30333/p2p/12D3KooWNE4LBfkYB2B7D4r9vL54YMMGsfAsXdkhWfBw8VHJSEQc /dns/crunchy.calamari.systems/tcp/30333/p2p/12D3KooWL3ELxcoMGA6han3wPQoym5DKbYHqkWkCuqyjaCXpyJTt /dns/hotdog.calamari.systems/tcp/30333/p2p/12D3KooWBdto53HnArmLdtf2RXzNWti7hD5mML7DWGZPD8q4cywv /dns/tasty.calamari.systems/tcp/30333/p2p/12D3KooWGs2hfnRQ3Y2eAoUyWKUL3g7Jmcsf8FpyhVYeNpXeBMSu /dns/tender.calamari.systems/tcp/30333/p2p/12D3KooWNXZeUSEKRPsp1yiDH99qSVawQSWHqG4umPjgHsn1joci -- --chain /usr/share/substrate/kusama.json'

sprawdź stan usługi kalmary:
#!/bin/bash

systemctl status calamari.service



Klucze sesji zbieracza (aura)
do zestawienia wymagane są dwa konta/klucze w danym momencie.

Konto collator: jest to konto, na którym znajduje się kaucja collator bond w wysokości 400 000 KMA. jest to również konto, na które zostanie zdeponowana część opłat transakcyjnych pobierającego. obligacja nie może zostać wydana podczas zestawiania konta. klucze dla tego konta powinny być starannie chronione i nigdy nie powinny istnieć w systemie plików węzła sortowania.
klucz sesji aura: jest to jednorazowe konto używane przez węzeł sortowania do tworzenia bloków. jest powiązany z kontem kolatora. dobrą praktyką jest regularne zmienianie klucza sesji i maksymalnie raz na sesję. substrat przechowuje klucze dla tego konta w magazynie kluczy parachain w systemie plików węzła sortującego(/var/lib/substrate/chains/calamari/keystore) gdy wywoływana jest jedna z metod rpc author_insertKey lub author_rotateKeys.
:::note obie z poniższych metod (wstaw, obróć) używają niebezpiecznego wywołania RPC w celu ustawienia klucza sesji węzła. musisz zatrzymać usługę, jeśli jest uruchomiona, a następnie uruchom węzeł za pomocą --rpc-methods=unsafe ustawienie parametrów, aby wywołania zakończyły się sukcesem. nie zapomnij później zmienić tego ustawienia z powrotem na bezpieczne, ponieważ węzeł, który umożliwia niebezpieczne wywołania RPC i ma ujawniony port RPC, może łatwo zmienić swoje klucze sesji przez każdego, co powoduje, że opłaty transakcyjne są wypłacane w innym miejscu niż zamierzone . ::: 

to polecenie demonstruje wstawianie klucza sesji za pomocą klucza utworzonego za pomocą https://docs.substrate.io/v3/tools/subkey/

wygeneruj klucz aury z podkluczem
#!/bin/bash

subkey generate \
  --scheme sr25519 \
  --network calamari \
  --output-type json \
  --words 12 \
  > ./aura.json

utwórz ładunek author_insertKey rpc
#!/bin/bash

echo '{
    "jsonrpc":"2.0",
    "id":1,
    "method":"author_insertKey",
    "params": [
      "aura",
      "<mnemonic phrase>",
      "<public key>"
    ]
  }' | jq \
    --arg mnemonic "$(jq -r .secretPhrase ./aura.json)" \
    --arg public "$(jq -r .publicKey ./aura.json)" \
    '. | .params[1] = $mnemonic | .params[2] = $public' > ./payload.json

wykonaj ładunek rpc author_insertKey
#!/bin/bash

curl \
  --header 'Content-Type: application/json;charset=utf-8' \
  --data @./payload.json \
  http://localhost:9133

potwierdzenie walidacji: opcjonalnie sprawdź, czy mnemonik aury używany przez węzeł pasuje do wygenerowanego
#!/bin/bash

sudo -H -u manta cat /var/lib/substrate/chains/calamari/keystore/$(sudo -H -u manta ls /var/lib/substrate/chains/calamari/keystore/)

potwierdzenie walidacji: opcjonalnie sprawdź, czy logi usługi pokazują, że węzeł działa z rolą: AUTHORITY (sprawdź sygnatury czasowe)
#!/bin/bash

journalctl -u calamari.service -g AUTHORITY

clean up: usuń sekrety z systemu plików, które zostały utworzone podczas generowania i dla ładunku
#!/bin/bash

rm ./aura.json ./payload.json

to polecenie demonstruje rotację klucza sesji. jeśli nie istnieje klucz sesji, jest on tworzony.
#!/bin/bash

curl -H 'Content-Type: application/json' --data '{ "jsonrpc":"2.0", "method":"author_rotateKeys", "id":1 }' http://localhost:9933

wynik wywołania RPC powinien wyglądać tak (właściwość result zawiera szesnastkową reprezentację klucza publicznego konta sesji aura):
{"jsonrpc":"2.0","result":"0x06736e65ab33fd1e4e3e434a1fa2c5425f0e263ddb50e6aeb15951288c562f61","id":1}

powiąż konto collator z kluczem sesji aura
:::uwaga, jeśli dzienniki węzłów sortowania nie zawierają obu [Relaychain] 💤 Idle oraz [Parachain] 💤 Idle wiadomości, Twój węzeł nadal się synchronizuje. nie wiąż konta sortującego z kluczem sesji aura dla węzła, którego synchronizacja jest niekompletna. spowoduje to wysunięcie kolatora. :::

wiązanie konta odbywa się w sieci. najprostszym sposobem na to jest użycie polkadot.js. 

Załaduj https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fws.calamari.systems%2F#/extrinsics w przeglądarce:https://github.com/Manta-Network/docs/blob/main/img/collator-program/session.setkeys.png

w pierwszym polu, oznaczonym „korzystając z wybranego konta”, wybierz konto kolatorskie, na którym znajduje się 400 000 obligacji kolatorskiej KMA.
w drugim (rozwijanym) polu oznaczonym „prześlij następujące zewnętrzne” wybierz sesję.
w trzecim (rozwijanym) polu wybierz setKeys(keys, proof)
zarówno w czwartym jak i piątym pudełku oznaczonym aura:SpConsensusAuraSr25519AppSr25519Public and proof: Bytes wprowadź szesnastkowy klucz publiczny klucza sesji aura.

kliknij przycisk Prześlij transakcję i poczekaj na potwierdzenie (zielony haczyk), który pojawi się w prawym górnym rogu okna przeglądarki.|

sprawdź, czy konto Collator i klucz sesji aura są powiązane przez ładowanie https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Fws.calamari.systems%2F#/chainstate w przeglądarce https://github.com/Manta-Network/docs/blob/main/img/collator-program/session.nextkeys.png 

n pierwszym (rozwijanym) polu oznaczonym "wybrane zapytanie stanu", wybierz session

w drugim (rozwijanym) polu wybierz nextKeys(AccountId32): Option<CalamariRuntimeOpaqueSessionKeys>

w trzecim (rozwijanym) polu wybierz konto collator, na którym znajduje się 400 000 obligacji collator KMA.

zostawić include option zaznaczone pole wyboru.

zostawić blockhash to query at pole ustawione na domyślną wartość 0x.

kliknij małą ikonę plusa (+) po prawej stronie drugiego menu rozwijanego.

sprawdź, czy nowe pudełko jest oznaczone session.nextKeys(AccountId32): Option<CalamariRuntimeOpaqueSessionKeys> pojawia się i zawiera obiekt json, którego wartość aura jest ustawiona na wygenerowany wcześniej szesnastkowy klucz publiczny sesji aura.


Synchronizuj

jeśli nie masz peerów w łańcuchu przekaźnikowym lub twój węzeł nie weryfikuje nowych bloków, upewnij się, że zegar twojego węzła jest dokładny, tj. poprzez synchronizację z serwerem czasu ntp.

musisz zsynchronizować zarówno łańcuch kalmarów parachain, jak i łańcuch przekaźników kusama, zanim zostanie przekazany ruch, aby uwzględnić twój kolator. całkowicie zsynchronizowane węzły blockchain substratu pokażą w swoich dziennikach stan bezczynności zarówno dla [Relaychain], jak i [Parachain] i wygląda to tak:

2022-03-01 17:18:58 [Parachain] 💤 Idle (49 peers), best: #1037783 (0xa0c5…04a8), finalized #1037781 (0xabd5…1c05), ⬇ 16.7kiB/s ⬆ 14.5kiB/s
2022-03-01 17:18:55 [Relaychain] 💤 Idle (49 peers), best: #11619808 (0x24a5…ad58), finalized #11619804 (0xa362…2df4), ⬇ 478.0kiB/s ⬆ 520.5kiB/s

jeśli twoje dzienniki węzłów sortowania nie zawierają wiadomości [Relaychain] 💤 Idle i [Parachain] 💤 Idle, twój węzeł nadal się synchronizuje. nie wiąż konta sortującego z kluczem sesji aura dla węzła, którego synchronizacja jest niekompletna. spowoduje to wysunięcie kolatora.

najlepszym sposobem na synchronizację jest po prostu uruchomienie węzła, aż w dziennikach pojawią się bezczynne komunikaty. może to potrwać do 2 tygodni, ale zapewni również doskonałą, zweryfikowaną kryptograficznie i pełną historię synchronizowanych łańcuchów bloków.

jeśli nie możesz czekać na zakończenie zalecanego mechanizmu synchronizacji, możesz uzyskać kopię szybkiej synchronizacji łańcuchów bloków kalmarów i kusama pobraną z węzłów zapasowych manta. aby to zrobić:

zatrzymaj usługę kalmarów
usuń bazy danych kalmarów i kusama ze ścieżki podstawowej (uważaj, aby nie usunąć swoich magazynów kluczy, które również znajdują się pod ścieżką podstawową)
pobierz kopię łańcucha bloków, w razie potrzeby wyodrębnij
upewnij się, że cała ścieżka bazowa i cała jej zawartość są własnością użytkownika, na którym działa Twój węzeł (w razie potrzeby rekursywnie zmień właściciela)
uruchom usługę kalmarów
sprawdź, czy węzeł synchronizuje się poprawnie
poczekaj, aż w dziennikach pojawią się komunikaty o bezczynności zarówno parachain, jak i relay-chain

polecenia szybkiej synchronizacji (wymaga aws cli):
#!/bin/bash

# stop calamari service
sudo systemctl stop calamari.service

# sync calamari blockchain database
sudo -H -u manta aws s3 sync --region eu-central-1 --no-sign-request --delete s3://calamari-kusama/var/lib/substrate/chains/calamari/db/full /var/lib/substrate/chains/calamari/db/full

# sync kusama blockchain database
sudo -H -u manta aws s3 sync --region eu-central-1 --no-sign-request --delete s3://calamari-kusama/var/lib/substrate/polkadot/chains/ksmcc3/db/full /var/lib/substrate/polkadot/chains/ksmcc3/db/full

# update database `current` manifests
sudo -H -u manta bash -c 'basename $(ls /var/lib/substrate/chains/calamari/db/full/MANIFEST-*) > /var/lib/substrate/chains/calamari/db/full/CURRENT'
sudo -H -u manta bash -c 'basename $(ls /var/lib/substrate/polkadot/chains/ksmcc3/db/full/MANIFEST-*) > /var/lib/substrate/polkadot/chains/ksmcc3/db/full/CURRENT'
sudo -H -u manta bash -c 'basename $(ls /var/lib/substrate/polkadot/chains/ksmcc3/db/full/parachains/db/MANIFEST-*) > /var/lib/substrate/polkadot/chains/ksmcc3/db/full/parachains/db/CURRENT'

Czekać

Upewnij się, że ukończyłeś https://docs.google.com/forms/d/e/1FAIpQLScizDDMq7jWeOPVVEMr3EY_Z6N6ugdkL8aKgAbZ9lAJX6DEOQ/viewform

Formularz. Jeśli zostanie zaakceptowana, rada Calamari złoży wniosek o wypromowanie Cię jako kandydata.

:::note Kandydat nie oznacza, że ​​Twój węzeł jest kolatorem. Na przykład, jeśli są 3 miejsca kandydackie, a inni kandydaci zajmują wszystkie miejsca, podczas gdy ty jesteś na 4 pozycji, musisz poczekać, aż węzeł zostanie wyrejestrowany lub rada otworzy nowe miejsca porównywarki. :::
Jeśli Twój zbieracz dostanie miejsce, dwie sesje (od 12 do 24 godzin) po przejściu wniosku o kandydaturę, zobaczysz w eksploratorze bloki wyprodukowane przez Twój zbieracz.
