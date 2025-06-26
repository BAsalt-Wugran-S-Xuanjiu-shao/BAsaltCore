# 配置文件说明 (config.json)

## 主要配置项：

```json
{
  // 温度变化平滑系数 (0.1为较平缓变化，值越大变化越快)
  "temperatureCurveFactor": 0.1,

  // 温度极限设置
  "TemperatureLimits": {
      "min": -15,
      "max": 45,
      "warningThreshold": 5  // 警告阈值（默认5度）
    },
  "DamageSettings": {
      "damageInterval": 2000, // 伤害间隔（毫秒）
      "freezeDamage": 4,
      "burnDamage": 4
    },
  "ProtectionSettings": {
      "deathProtectionDuration": 60000,  // 死亡保护持续时间（毫秒）
      "initialProtectionDuration": 30000,  // 初始进入保护持续时间（毫秒）
      "biomeTemperatureDelay": 5000,  // 群系温度延迟生效时间（毫秒）
    },

  // 温度效果配置
  "TemperatureEffects": {
    "coldEffects": [ // 低温效果
      {
        "threshold": 10,   // 触发阈值(温度≤10°C时生效)
        "effectId": 2,     // 效果ID(2=移动加速)
        "amplifier": 1,    // 效果等级
        "duration": 60     // 持续时间(秒)
      },
      //...其他低温效果
    ],
    "hotEffects": [ // 高温效果
      {
        "threshold": 30,   // 触发阈值(温度≥30°C时生效)
        "effectId": 17,    // 效果ID(17=瞬间治疗)
        "amplifier": 0,    
        "duration": 60
      }
      //...其他高温效果
    ]
  },

  // 群系温度修正值
  "BiomeTemperatureModifiers": {
    "desert": 12,      // 沙漠+12°C
    "savanna": 8,      // 热带草原+8°C
    "snowy_tundra": -15, // 雪原-15°C
    //...其他群系修正
  },

  // 盔甲温度修正
  "ArmorTemperatureModifiers": {
    "minecraft:leather_chestplate": 6, // 皮革胸甲+6°C
    "minecraft:diamond_helmet": -3,   // 钻石头盔-3°C
    //...其他盔甲修正
  },

  // 食物温度效果
  "FoodTemperatureEffects": {
    "minecraft:cooked_beef": { 
      "tempChange": 8,     // 温度变化值
      "duration": 5000     // 持续时间(毫秒)
    },
    "minecraft:melon_slice": {
      "tempChange": -5,    // 食用西瓜降低10°C
      "duration": 4000
    }
    //...其他食物效果
  }
}
```

## 温度系统插件 API 文档
### 1. 插件概述
温度系统插件提供了玩家温度管理功能，其他插件可以通过API接口获取和修改玩家温度。

插件名称 : temperature 版本 : 1.0.0 作者 : 伊希娅 简介 : 管理玩家温度及相关效果的系统插件
 ### 2. API 接口说明
 #### a.获取玩家当前温度
 `getPlayerTemperature(player)`  
 **参数**：  
 - player: 玩家对象  
 **返回值**：当前温度值（Number）

 #### b.增加玩家温度
 `addPlayerTemperature(player, amount)`  
 **参数**：  
 - player: 玩家对象  
 - amount: 增加的温度值（Number）

 #### c.减少玩家温度
 `reducePlayerTemperature(player, amount)`  
 **参数**：  
 - player: 玩家对象  
 - amount: 减少的温度值（Number）

 #### d.设置玩家温度
 `setPlayerTemperature(player, temperature)`  
 **参数**：  
 - player: 玩家对象  
 - temperature: 目标温度值（Number）

 使用方法 : pauseTemperatureUpdate(player, pauseState)  
 参数 :
 - player: 玩家对象
 - pauseState: true(暂停)/false(恢复)
 返回值 : 无

 #### f.检查玩家是否受温度影响
 使用方法 : isAffectedByTemperature(player)  
 参数：
 - player: 玩家对象  
 返回值 : Boolean (true表示正在受温度影响)

 #### g.获取基础温度值
 使用方法 : getBaseTemperature(player)  
 参数：
 - player: 玩家对象  
 返回值 : 计算环境因素后的基础温度值
 
#### h.维持合适温度范围  
`maintainTemperature(player, [min=20], [max=25], [duration=0], [callback])`  
**参数**：  
- player: 玩家对象  
- min: 最低温度(默认20°C)  
- max: 最高温度(默认25°C)  
- duration: 持续时间(毫秒，0表示永久)  
- callback: 持续时间结束回调函数  

#### i.停止维持温度  
`stopMaintaining(player)`  
**参数**：  
- player: 玩家对象  

#### h.维持自定义温度范围  
`maintainTemperature(player, [min=20], [max=25], [duration=0], [callback])`  
**参数说明**：  
- `min`：最低温度（支持负数，默认20）  
- `max`：最高温度（默认25）  
- 参数顺序不影响，系统会自动排序区间  



### 插件示例:
 示例 :
 ```javascript
const temperature = require('./temperature');
const player = ...; // 获取玩家对象
const currentTemp = temperature.API.getPlayerTemperature(player);
console.log(`玩家当前温度: ${currentTemp}°C`);
// 增加玩家温度5°C
temperature.API.addPlayerTemperature(player, 5);

// 减少玩家温度5°C
temperature.API.reducePlayerTemperature(player, 5);

// 将玩家温度设置为25°C
temperature.API.setPlayerTemperature(player, 25);

// 暂停玩家温度更新
temperature.pauseTemperatureUpdate(player, true);

// 检查温度影响状态
if (!temperature.isAffectedByTemperature(player)) {
    // 设置自定义温度值
    temperature.setPlayerTemperature(player, 25);
}

// 获取环境基础温度
const baseTemp = temperature.getBaseTemperature(player);
console.log(`环境基础温度: ${baseTemp}°C`);

// 定时恢复温度系统
setTimeout(() => {
    temperature.pauseTemperatureUpdate(player, false);
    player.sendToast("温度系统已恢复");
}, 30000);

//维持玩家在22-28°C范围30秒
temperature.API.maintainTemperature(player, 22, 28, 30000, () => {
    player.sendToast('舒适温度维持结束！');
});

// 手动停止维持
temperature.API.stopMaintaining(player);

// 维持玩家在-5°C到10°C的低温环境60秒
temperature.API.maintainTemperature(player, 10, -5, 60000);

// 维持高温环境（50°C到60°C）
temperature.API.maintainTemperature(player, 60, 50);

// 维持精确温度25°C（设置相同值）
temperature.API.maintainTemperature(player, 25, 25);
```

