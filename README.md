blueprint:
  name: IR Remote → Multi Switch Control
  description: 用 Tasmota IR 遙控器多個按鍵控制多個 Home Assistant 開關
  domain: automation
  input:
    ir_topic:
      name: MQTT Topic
      description: Tasmota 發送 IR 訊息嘅 topic（例如 tele/tasmota_988F3E/RESULT）
      default: tele/tasmota_988F3E/RESULT
    ir_code_light:
      name: IR Code for Light
      description: 遙控器按鍵嘅 Data 值（例如 0x20DF8679）
    target_light:
      name: Target Light Switch
      selector:
        entity:
          domain: switch
    ir_code_fan:
      name: IR Code for Fan
      description: 遙控器按鍵嘅 Data 值（例如 0x20DF10EF）
    target_fan:
      name: Target Fan Switch
      selector:
        entity:
          domain: switch
    ir_code_ac:
      name: IR Code for AC
      description: 遙控器按鍵嘅 Data 值（例如 0x20DF40BF）
    target_ac:
      name: Target AC Switch
      selector:
        entity:
          domain: switch

trigger:
  - platform: mqtt
    topic: !input ir_topic

action:
  - choose:
      - conditions: "{{ value_json.IrReceived.Data == !input ir_code_light }}"
        sequence:
          - service: switch.toggle
            target:
              entity_id: !input target_light
      - conditions: "{{ value_json.IrReceived.Data == !input ir_code_fan }}"
        sequence:
          - service: switch.toggle
            target:
              entity_id: !input target_fan
      - conditions: "{{ value_json.IrReceived.Data == !input ir_code_ac }}"
        sequence:
          - service: switch.toggle
            target:
              entity_id: !input target_ac
mode: single
