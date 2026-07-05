# custom-esphome-components

個人使用的 ESPHome external components 集合。

## Components

### desky

支援 Desky 升降桌(sit/stand desk)的高度讀取與控制。詳見 [components/desky/README.md](components/desky/README.md)。

**來源**: component 取自 [ssieb/esphome_components](https://github.com/ssieb/esphome_components) 的 `components/desky/`,未修改功能邏輯。

完整範例(含腳位、memory 按鈕、坐站自動排程)見 [examples/standing-desk.yaml](examples/standing-desk.yaml)。

## 使用方式

在 ESPHome YAML 設定中加入:

```yaml
external_components:
  - source:
      type: git
      url: https://github.com/gene891212/custom-esphome-components
      ref: main
    components: [desky]
```

## License

本 repo 沿用原作者 [ssieb/esphome_components](https://github.com/ssieb/esphome_components) 的 [LICENSE](LICENSE)(ESPHome License,MIT + GPL)。
