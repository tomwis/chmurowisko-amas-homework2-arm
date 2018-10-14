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

`<globally-unique-name><number>`

## Część 2 - ARM Template

Składowe szablonu:
- Jedna sieć wirtualna
- 2 podsieci z NSG
- W każdej podsieci 1 maszyna wirtualna z Ubuntu 18.04

## Część 3 - własna rola RBAC

Zbudowałem własną rolę z następującymi uprawnieniami:
- uruchamianie maszyny
- zatrzymywanie maszyny
- wysyłanie zgłoszenia do supportu przez Portal Azure