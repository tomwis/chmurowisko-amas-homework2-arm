# Deploying resources with Azure Resource Manager - Homework
## Cześć 1 - Konwencja nazewnicza
### Grupy zasobów

`<service-short-name>-<environment>-rg-<number>`

- service-short-name - skrócona nazwa usługi, dla której tworzymy grupę, 5 znaków
- environment - środowisko (prod, staging, test, dev, exp (experimental)), 3 znaki (3 pierwsze litery lub skrót)
- number - numer grupy, w razie gdybyśmy mieli więcej niż jedną instancję danego środowiska, 2 cyfry

### Sieci wirtualne

`<service-short-name>-vnet`

- service-short-name - skrócona nazwa usługi, dla której tworzymy sieć, 5 znaków

### Maszyny wirtualne

`<service-short-name>-<role>-vm<number>`

- service-short-name - skrócona nazwa usługi, dla której tworzymy maszynę, 5 znaków
- role - do czego służy maszyna, np. sql, web, red (redis), mon (mongodb), etc., 3 znaki
- number - 3 cyfry

### Dyski

`<vm-name>-osdisk`

- vm-name - nazwa maszyny wirtualnej, do której przypisany jest dysk

### Konta składowania danych

`<short-service-name><role>st<number>`

- service-short-name - skrócona nazwa usługi, dla której tworzymy konto, 5 znaków
- role - do czego służy konto, np. dane, obrazki, itp., 5 znaków
- number - 3 cyfry

Definicje reguł polityk znajdują się w folderze [policies](/policies), a w pliku [deploy.azcli](deploy.azcli) tworzone są same polityki i ich przypisania.

## Część 2 - ARM Template

Składowe szablonu:
- Sieć wirtualna
- 2 podsieci z ustawionymi network security group
- W każdej podsieci 1 maszyna wirtualna z Ubuntu 18.04 i publicznym ip

Szablon wykorzystuje linked templates i jest podzielony na 3 części:
- Tworzenie sieci wirtualnej, nsg i podsieci - [arm-templates/vnet-2-subnets.json](/arm-templates/vnet-2-subnets.json)
- Tworzenie interfejsu sieciowego, publicznego ip i maszyny wirtualnej w podsieci - [arm-templates/vm-in-subnet.json](/arm-templates/vm-in-subnet.json)
- Powyższe 2 części są połączone w jednym nadrzędnim szablonie. Drugi szablon, z tworzeniem maszyny wirtualnej, jest wykorzystany 2 razy z innymi parametrami, tak aby w każdej podsieci stworzyć jedną maszynę. - [arm-templates/deploy-2-ubuntu-vms.json](arm-templates/deploy-2-ubuntu-vms.json)

W [deploy.azcli](deploy.azcli) mamy polecenia do stworzenia grupy i zasobów z szablonu.

## Część 3 - własna rola RBAC

Zbudowałem własną rolę z następującymi uprawnieniami:
- uruchamianie maszyny
- zatrzymywanie maszyny (tylko wyłączanie - powerOff lub wyłączanie ze zwalnianiem zasobów obliczeniowych - deallocate)
- wysyłanie zgłoszenia do supportu przez Portal Azure

Definicja roli dostępna w [rbac/virtual-machine-basic-operator.json](rbac/virtual-machine-basic-operator.json). W [deploy.azcli](deploy.azcli) rola jest tworzona.

## Część 4 - Użycie KeyVault

W [deploy.azcli](deploy.azcli) mamy polecenia do stworzenia grupy, KeyVaulta z sekretami dla loginu i hasła admina na maszynach wirtualnych oraz zasobów z szablonu, gdzie w parameterach wykorzystujemy referencje do KeyVaulta - [arm-templates/deploy-2-ubuntu-vms-keyvault.parameters.json](arm-templates/deploy-2-ubuntu-vms-keyvault.parameters.json)
