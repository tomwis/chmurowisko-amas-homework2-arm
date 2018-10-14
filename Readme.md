# Deploying resources with Azure Resource Manager - Homework
## Cześć 1 - Konwencja nazewnicza
### Grupy zasobów

`<service-short-name>-<environment>-rg-<number>`

- service-short-name - skrócona nazwa usługi, dla której tworzymy grupę, 5 znaków
- environment - środowisko, możliwe wartości: prod, staging, test, dev, exp (experimental)
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

### Konta składowania danych

`<short-service-name><role>st<number>`

- service-short-name - skrócona nazwa usługi, dla której tworzymy konto, 5 znaków
- role - do czego służy konto, np. dane, obrazki, itp., 5 znaków
- number - 3 cyfry

## Część 2 - ARM Template

Składowe szablonu:
- Sieć wirtualna
- 2 podsieci z ustawionymi network security group
- W każdej podsieci 1 maszyna wirtualna z Ubuntu 18.04 i publicznym ip

Szablon wykorzystuje linked templates i jest podzielony na 2 części:
- Tworzenie sieci wirtualnej, nsg i podsieci
- Tworzenie interfejsu sieciowego, publicznego ip i maszyny wirtualnej w podsieci

Powyższe 2 części są połączone w jednym nadrzędnim szablonie. Drugi szablon, z tworzeniem maszyny wirtualnej, jest wykorzystany 2 razy z innymi parametrami, tak aby w każdej podsieci stworzyć jedną maszynę. 

## Część 3 - własna rola RBAC

Zbudowałem własną rolę z następującymi uprawnieniami:
- uruchamianie maszyny
- zatrzymywanie maszyny (tylko wyłączanie - powerOff lub wyłączanie ze zwalnianiem zasobów obliczeniowych - deallocate)
- wysyłanie zgłoszenia do supportu przez Portal Azure

## Część 4 - Użycie KeyVault