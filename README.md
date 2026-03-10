# Home Assistant + Xiaomi 智能家居配置

**创建日期：** 2026-03-08  
**最后更新：** 2026-03-08

---

## 📋 配置概览

| 项目 | 值 |
|------|-----|
| HA 地址 | `http://192.168.31.35:8123` |
| 集成方式 | Xiaomi Miot (HACS) |
| 部署方式 | Docker (NAS) |

---

## 🎯 设备实体列表

### 小爱音箱 Pro（客厅）

| 实体 ID | 类型 | 功能 |
|--------|------|------|
| `media_player.xiaomi_lx06_3ff3_play_control` | media_player | 播放控制 |
| `text.xiaomi_lx06_3ff3_execute_text_directive` | text | 执行文本指令 |
| `text.xiaomi_lx06_3ff3_play_text` | text | 播放文本 |
| `button.xiaomi_lx06_3ff3_play_music` | button | 播放音乐 |
| `button.xiaomi_lx06_3ff3_wake_up` | button | 唤醒 |

### 空调（客厅）- miir.aircondition.ir02

| 实体 ID | 类型 | 功能 |
|--------|------|------|
| `button.miir_ir02_8112_turn_off` | button | 关闭空调 |
| `button.miir_ir02_8112_turn_on` | button | 打开空调 |
| `button.miir_ir02_8112_temperature_up` | button | 温度 + |
| `button.miir_ir02_8112_temperature_down` | button | 温度 - |
| `button.miir_ir02_8112_fan_speed_up` | button | 风速 + |
| `button.miir_ir02_8112_fan_speed_down` | button | 风速 - |
| `climate.miir_ir02_8112_ir_aircondition_control` | climate | 空调控制 |
| `number.miir_ir02_8112_temperature_for_ir` | number | 温度设置 (16-30°C) |
| `select.miir_ir02_8112_mode_for_ir` | select | 模式选择 |

**模式选项：** Auto, Cool, Heat, Dry, Fan

### 其他设备

| 设备 | 实体 ID | 类型 |
|------|--------|------|
| 米家智能显示器灯条 1S | `light.yeelink_lamp22_1fe0_light` | light |
| 米家无雾加湿器 3 | `humidifier.xiaomi_4lite_097f_humidifier` | humidifier |
| 米家智能眼部按摩仪 | `switch.xiaomi_my02_5d42_massager` | switch |

---

## 🔑 Access Token

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJlYWZiM2JhZWYxNWQ0YjRmYThiNmUzZjY5Zjk1OTNkNyIsImlhdCI6MTc3Mjk4MDEwNywiZXhwIjoyMDg4MzQwMTA3fQ.nYqC_v9uL3x6BtNwpetr
```

⚠️ **注意：** Token 是敏感信息，不要公开分享

---

## 🛠️ API 调用示例

### 关闭空调

```bash
curl -X POST \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{"entity_id": "button.miir_ir02_8112_turn_off"}' \
  http://192.168.31.35:8123/api/services/button/press
```

### 打开空调

```bash
curl -X POST \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{"entity_id": "button.miir_ir02_8112_turn_on"}' \
  http://192.168.31.35:8123/api/services/button/press
```

### 设置空调温度

```bash
curl -X POST \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{"entity_id": "number.miir_ir02_8112_temperature_for_ir", "value": 26}' \
  http://192.168.31.35:8123/api/services/number/set_value
```

### 小爱同学播放音乐

```bash
curl -X POST \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{"entity_id": "text.xiaomi_lx06_3ff3_execute_text_directive", "value": "播放音乐天后"}' \
  http://192.168.31.35:8123/api/services/text/set_value
```

---

## 🤖 OpenClaw Skill

**Skill 名称：** `ha-xiaomi-control`  
**位置：** `~/.openclaw/workspace/skills/ha-xiaomi-control.skill`

### 触发规则

| 触发词 | 动作 |
|--------|------|
| "小爱同学" | 调用小爱音箱执行指令 |
| "小爱" | 调用小爱音箱执行指令 |
| "空调" | 调用 HA API 控制空调 |

### 使用示例

```
用户：小爱同学 播放音乐 天后
→ 执行：text.xiaomi_lx06_3ff3_execute_text_directive

用户：关闭空调
→ 执行：button.miir_ir02_8112_turn_off

用户：空调 26 度
→ 执行：number.miir_ir02_8112_temperature_for_ir set_value 26
```

---

## 📦 安装步骤回顾

### 1. 安装 HACS

```bash
docker exec -it homeassistant bash
wget -O - https://get.hacs.vip | bash -
docker restart homeassistant
```

### 2. 安装 Xiaomi Miot 集成

- HA 网页 → HACS → 集成 → 浏览并下载存储库
- 搜索 `Xiaomi Miot` → 下载 `hass-xiaomi-miot`
- 重启 HA

### 3. 配置集成

- HA → 设置 → 设备与服务 → 添加集成
- 搜索 `Xiaomi Miot`
- 登录小米账号
- 选择设备

### 4. 创建 Access Token

- HA → 个人资料 → 长期访问令牌 → 创建令牌
- 名称：`openclaw`
- 复制保存

---

## 🔧 故障排查

| 问题 | 解决方案 |
|------|---------|
| 连接失败 | 检查 HA 是否运行，`docker ps \| grep homeassistant` |
| 401 未授权 | Token 过期，重新创建 |
| 实体不存在 | 检查实体 ID 是否正确，设备是否在线 |
| 设备离线 | 检查米家 APP 中设备状态 |

---

## 📚 相关链接

- [Home Assistant 文档](https://www.home-assistant.io/docs/)
- [Xiaomi Miot GitHub](https://github.com/al-one/hass-xiaomi-miot)
- [HACS 官网](https://hacs.xyz/)

---

## 📝 备注

- 空调是红外控制（miir.aircondition.ir02），通过小爱音箱 Pro 发射红外信号
- 设备位置：客厅
- 配置完成日期：2026-03-08
