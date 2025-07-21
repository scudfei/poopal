# API 接口文档

## 概述

Poopal API 提供完整的RESTful接口，支持设备管理、用户认证、数据分析等功能。所有API均采用JSON格式进行数据交互。

## 基础信息

- **Base URL**: `https://api.poopal.com/v1`
- **认证方式**: Bearer Token (JWT)
- **内容类型**: `application/json`
- **字符编码**: UTF-8

## 认证

### JWT Token获取

```http
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "password123"
}
```

**响应示例**:
```json
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": 1,
      "email": "user@example.com",
      "username": "john_doe"
    },
    "expires_in": 3600
  }
}
```

### 认证头部

所有需要认证的请求都必须在请求头中包含：

```http
Authorization: Bearer <your_jwt_token>
```

## 用户管理

### 用户注册

```http
POST /users/register
Content-Type: application/json

{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "password123",
  "phone": "+86-13800138000"
}
```

### 获取用户信息

```http
GET /users/profile
Authorization: Bearer <token>
```

**响应示例**:
```json
{
  "success": true,
  "data": {
    "id": 1,
    "username": "john_doe",
    "email": "john@example.com",
    "phone": "+86-13800138000",
    "created_at": "2024-01-01T00:00:00Z",
    "pets": [
      {
        "id": 1,
        "name": "小白",
        "breed": "金毛",
        "age": 2,
        "weight": 25.5
      }
    ]
  }
}
```

### 更新用户信息

```http
PUT /users/profile
Authorization: Bearer <token>
Content-Type: application/json

{
  "username": "new_username",
  "phone": "+86-13900139000"
}
```

## 宠物管理

### 添加宠物

```http
POST /pets
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "小白",
  "breed": "金毛犬",
  "age": 2,
  "weight": 25.5,
  "gender": "male",
  "description": "活泼可爱的金毛"
}
```

### 获取宠物列表

```http
GET /pets
Authorization: Bearer <token>
```

### 获取宠物详情

```http
GET /pets/{pet_id}
Authorization: Bearer <token>
```

### 更新宠物信息

```http
PUT /pets/{pet_id}
Authorization: Bearer <token>
Content-Type: application/json

{
  "weight": 26.0,
  "description": "训练进步很大"
}
```

### 删除宠物

```http
DELETE /pets/{pet_id}
Authorization: Bearer <token>
```

## 设备管理

### 设备注册

```http
POST /devices
Authorization: Bearer <token>
Content-Type: application/json

{
  "device_id": "POOPAL-001-ABC123",
  "pet_id": 1,
  "location": "客厅",
  "timezone": "Asia/Shanghai"
}
```

### 获取设备列表

```http
GET /devices
Authorization: Bearer <token>
```

**响应示例**:
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "device_id": "POOPAL-001-ABC123",
      "pet_id": 1,
      "pet_name": "小白",
      "location": "客厅",
      "status": "online",
      "firmware_version": "1.2.3",
      "battery_level": 85,
      "last_seen": "2024-01-01T12:00:00Z",
      "created_at": "2024-01-01T00:00:00Z"
    }
  ]
}
```

### 获取设备详情

```http
GET /devices/{device_id}
Authorization: Bearer <token>
```

### 更新设备设置

```http
PUT /devices/{device_id}/settings
Authorization: Bearer <token>
Content-Type: application/json

{
  "reward_settings": {
    "food_amount": 3,
    "sound_enabled": true,
    "sound_volume": 70,
    "led_brightness": 80
  },
  "detection_settings": {
    "sensitivity": 85,
    "timeout": 30
  }
}
```

### 设备控制命令

```http
POST /devices/{device_id}/commands
Authorization: Bearer <token>
Content-Type: application/json

{
  "command": "dispense_food",
  "params": {
    "amount": 2
  }
}
```

**支持的命令**:
- `dispense_food`: 手动投食
- `play_sound`: 播放声音
- `test_sensors`: 传感器测试
- `calibrate`: 设备校准
- `reboot`: 重启设备

## 行为数据

### 上传行为记录

```http
POST /behaviors
Authorization: Bearer <token>
Content-Type: application/json

{
  "device_id": "POOPAL-001-ABC123",
  "pet_id": 1,
  "behavior_type": "correct",
  "confidence": 0.95,
  "timestamp": "2024-01-01T12:00:00Z",
  "sensor_data": {
    "weight": 25.5,
    "temperature": 36.5,
    "position": {"x": 100, "y": 150}
  }
}
```

### 获取行为记录

```http
GET /behaviors?pet_id=1&start_date=2024-01-01&end_date=2024-01-31&page=1&limit=50
Authorization: Bearer <token>
```

**查询参数**:
- `pet_id`: 宠物ID
- `device_id`: 设备ID
- `behavior_type`: 行为类型 (correct/incorrect/unknown)
- `start_date`: 开始日期 (YYYY-MM-DD)
- `end_date`: 结束日期 (YYYY-MM-DD)
- `page`: 页码 (默认1)
- `limit`: 每页数量 (默认20，最大100)

### 获取行为统计

```http
GET /behaviors/statistics?pet_id=1&period=week
Authorization: Bearer <token>
```

**响应示例**:
```json
{
  "success": true,
  "data": {
    "period": "week",
    "start_date": "2024-01-01",
    "end_date": "2024-01-07",
    "total_events": 156,
    "correct_events": 142,
    "incorrect_events": 14,
    "success_rate": 91.03,
    "daily_breakdown": [
      {
        "date": "2024-01-01",
        "total": 22,
        "correct": 20,
        "incorrect": 2,
        "success_rate": 90.91
      }
    ],
    "trend": "improving"
  }
}
```

### 获取行为分析报告

```http
GET /behaviors/analysis/{pet_id}?period=month
Authorization: Bearer <token>
```

## 训练管理

### 创建训练计划

```http
POST /training/plans
Authorization: Bearer <token>
Content-Type: application/json

{
  "pet_id": 1,
  "name": "基础定点训练",
  "description": "针对小白的基础定点排便训练",
  "duration_days": 30,
  "settings": {
    "reward_frequency": "immediate",
    "sound_enabled": true,
    "food_reward": true
  }
}
```

### 获取训练计划

```http
GET /training/plans?pet_id=1
Authorization: Bearer <token>
```

### 更新训练进度

```http
PUT /training/plans/{plan_id}/progress
Authorization: Bearer <token>
Content-Type: application/json

{
  "completed_days": 7,
  "notes": "第一周训练效果良好，成功率明显提升"
}
```

## 通知管理

### 获取通知列表

```http
GET /notifications?page=1&limit=20&unread_only=true
Authorization: Bearer <token>
```

### 标记通知已读

```http
PUT /notifications/{notification_id}/read
Authorization: Bearer <token>
```

### 通知设置

```http
PUT /notifications/settings
Authorization: Bearer <token>
Content-Type: application/json

{
  "email_enabled": true,
  "push_enabled": true,
  "training_alerts": true,
  "device_alerts": true,
  "weekly_reports": true
}
```

## 数据导出

### 导出行为数据

```http
GET /export/behaviors?pet_id=1&format=csv&start_date=2024-01-01&end_date=2024-01-31
Authorization: Bearer <token>
```

**支持格式**:
- `csv`: CSV格式
- `json`: JSON格式
- `pdf`: PDF报告

### 导出训练报告

```http
GET /export/training-report/{pet_id}?format=pdf&period=month
Authorization: Bearer <token>
```

## WebSocket 实时通信

### 连接地址

```
wss://api.poopal.com/ws
```

### 认证

连接时需要在URL参数中提供token：

```
wss://api.poopal.com/ws?token=<your_jwt_token>
```

### 消息格式

#### 订阅设备数据

```json
{
  "type": "subscribe",
  "channel": "device_data",
  "device_id": "POOPAL-001-ABC123"
}
```

#### 接收实时数据

```json
{
  "type": "device_data",
  "device_id": "POOPAL-001-ABC123",
  "timestamp": "2024-01-01T12:00:00Z",
  "data": {
    "status": "online",
    "battery_level": 85,
    "sensor_readings": {
      "weight": 25.5,
      "temperature": 36.5
    }
  }
}
```

#### 行为事件推送

```json
{
  "type": "behavior_event",
  "pet_id": 1,
  "device_id": "POOPAL-001-ABC123",
  "behavior_type": "correct",
  "confidence": 0.95,
  "timestamp": "2024-01-01T12:00:00Z"
}
```

## 错误处理

### 错误格式

```json
{
  "success": false,
  "error": {
    "code": "INVALID_REQUEST",
    "message": "请求参数不正确",
    "details": {
      "field": "email",
      "reason": "邮箱格式不正确"
    }
  }
}
```

### 常见错误码

| 错误码 | HTTP状态码 | 描述 |
|--------|------------|------|
| INVALID_REQUEST | 400 | 请求参数不正确 |
| UNAUTHORIZED | 401 | 未授权访问 |
| FORBIDDEN | 403 | 禁止访问 |
| NOT_FOUND | 404 | 资源不存在 |
| CONFLICT | 409 | 资源冲突 |
| RATE_LIMIT_EXCEEDED | 429 | 请求频率超限 |
| INTERNAL_ERROR | 500 | 服务器内部错误 |
| SERVICE_UNAVAILABLE | 503 | 服务不可用 |

## 限制说明

### 请求频率限制

- **认证接口**: 10次/分钟
- **数据上传**: 100次/分钟
- **查询接口**: 1000次/小时
- **文件上传**: 10MB/文件，100MB/天

### 分页限制

- 默认页大小: 20
- 最大页大小: 100
- 最大查询时间跨度: 90天

## SDK 示例

### JavaScript/Node.js

```javascript
const PoopalAPI = require('poopal-sdk');

const client = new PoopalAPI({
  baseURL: 'https://api.poopal.com/v1',
  token: 'your_jwt_token'
});

// 获取设备列表
const devices = await client.devices.list();

// 获取行为统计
const stats = await client.behaviors.getStatistics({
  petId: 1,
  period: 'week'
});
```

### Python

```python
from poopal_sdk import PoopalAPI

client = PoopalAPI(
    base_url='https://api.poopal.com/v1',
    token='your_jwt_token'
)

# 获取设备列表
devices = client.devices.list()

# 上传行为数据
client.behaviors.create({
    'device_id': 'POOPAL-001-ABC123',
    'pet_id': 1,
    'behavior_type': 'correct',
    'confidence': 0.95
})
```

## 更新日志

### v1.0.0 (2024-01-01)
- 初始版本发布
- 支持基本的设备管理和数据收集功能

### v1.1.0 (2024-02-01)
- 新增训练计划管理
- 支持WebSocket实时通信
- 增加数据导出功能

### v1.2.0 (2024-03-01)
- 优化行为识别算法
- 新增多宠物支持
- 改进通知系统
