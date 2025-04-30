# WebRTC-AqaraG3H-PTZ
Code Yaml pour l'intégration dans Home Assistant (HA) des fonctionnalités PTZ de la caméra Aqara Camera Hub G3 en utilisant une carte WebRTC.

## Pré-requis

Vous devez avoir effectué l'intégration de votre caméra Aqara G3H dans HA via les modules suivants :
- [Aqara Post](https://github.com/sdavides/AqaraPOST-Homeassistant)
- [Go2RTC](https://github.com/AlexxIT/go2rtc)
- [WebRTC Camera](https://github.com/AlexxIT/WebRTC)

Ces 3 modules doivent être fonctionnels.

Si d'autres modules ou une autre méthode ont été utilisés, vous devrez peut-être adapter le code ci-dessous mais il n'est pas garanti qu'il fonctionne.

## Fonctionnement
Le script yaml suivant est basé sur l'intégration AqaraPost créant 4 entités dans HA pour les contrôles Pan & Tilt (Panormatique et inclinaison) de la caméra Aqara G3H. Ces 4 entités sont :
- button.camera_g3_ptz_left
- button.camera_g3_ptz_right
- button.camera_g3_ptz_up
- button.camera_g3_ptz_down

Vous devez copier ce code dans un nouveau script : Depuis le menu de navigation de HA, Paramètres -> Automatisations -> Scripts -> Créer un nouveau script. Passez l'éditeur en mode yaml (click cliquez sur les 3 petits points en haut à droite ouis choisissez Yaml) et faire un copié-collé de ce qui suit. ENREGISTRER !

```yaml
alias: Camera G3H PTZ Controls
fields:
  direction:
    name: direction
    description: Direction PTZ
    example: left, right, up or down
sequence:
  - choose:
      - alias: SI direction LEFT
        conditions: "{{ direction == 'left' }}"
        sequence:
          - action: button.press
            target:
              entity_id: button.camera_g3_ptz_left
            data: {}
      - alias: SI direction RIGHT
        conditions: "{{ direction == 'right' }}"
        sequence:
          - action: button.press
            target:
              entity_id: button.camera_g3_ptz_right
            data: {}
      - alias: SI direction UP
        conditions: "{{ direction == 'up' }}"
        sequence:
          - action: button.press
            target:
              entity_id: button.camera_g3_ptz_up
            data: {}
      - alias: SI direction DOWN
        conditions:
          - condition: template
            value_template: "{{ direction == 'down' }}"
        sequence:
          - action: button.press
            target:
              entity_id: button.camera_g3_ptz_down
            data: {}

```

Vous pouvez ouvrir à nouveau le script et passer en mode visuel pour mieux analyser ce qui se passe. En faite, ce script attend une valeur dans un champs d'entrée nommé "direction". Par exemple, si cette valeur est "left", l'entité button.`button.camera_g3_ptz_down` sera actionnée. Ce qui aura pour effet de faire pivoter votre caméra vers la gauche. La valeur left, right, top ou down de ce champs est renseignée grâce à l'intégration WebRTC Camera qui va appelé ce script avec le paramètre "direction".

Depuis la carte WebRTC sur votre tableau de bord, vous pouvez finalement ajouter le paramètre suivante :

```yaml
ptz:
  service: script.camera_gh3_ptz_controls
  data_left:
    direction: left
  data_right:
    direction: right
  data_up:
    direction: up
  data_down:
    direction: down
```

`data_left`, `data_right`, `data_up` et `data_down` représentent le bouton directionnel présent sur la vue du flux de la caméra. On a bien appelé notre script script.camera_gh3_ptz_controls précédemment crée et envoyé le paramètre direction avec la valeur souhaitée. A présent, sur le bouton directionnel de la vue du flux de la caméra, si vous cliquez sur l'une des 4 directions.

A savoir, vous pouvez désactiver les directions que vous ne souhaitez pas utiliser. Par exemple, vous ne voudriez pas que la caméra s'incline vers le haut si elle est accroché à un plafond.

```yaml
ptz:
  service: script.camera_gh3_ptz_controls
  data_left:
    direction: left
  data_right:
    direction: right
  data_down:
    direction: down
```
A présent, si vous cliquez sur ( **↑** ), rien ne se passe.

Vous pouvez maintenant profité de la fonctionnalité Pan & Tilt de votre Aqara G3H et la contrôler à votre guise depuis une carte WebRTC.
