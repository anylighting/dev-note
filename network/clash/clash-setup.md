



# clash setup




## extension scripts

```javascript
// Define main function (script entry)
// 规则集通用配置
const ruleProviderCommon = {
  "type": "http",
  "format": "yaml",
  "interval": 86400
};
 
// 规则集配置
const ruleProviders = {
  "reject": {
    ...ruleProviderCommon,
    "behavior": "domain",
    "url": "https://fastly.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/reject.txt",
    "path": "./ruleset/loyalsoldier/reject.yaml"
  },
  "openai": {
    ...ruleProviderCommon,
    "behavior": "classical",
    "url": "https://fastly.jsdelivr.net/gh/blackmatrix7/ios_rule_script@master/rule/Clash/OpenAI/OpenAI.yaml",
    "path": "./ruleset/blackmatrix7/openai.yaml"
  }
}

// 规则
const rules = [
  // 自定义规则
  //"DOMAIN-SUFFIX,googleapis.cn,节点选择", // Google服务
  //"DOMAIN-SUFFIX,gstatic.com,节点选择", // Google静态资源
  //"DOMAIN-SUFFIX,xn--ngstr-lra8j.com,节点选择", // Google Play下载服务
  //"DOMAIN-SUFFIX,github.io,节点选择", // Github Pages
  //"DOMAIN,v2rayse.com,节点选择", // V2rayse节点工具
  // blackmatrix7 规则集
  //"RULE-SET,openai,ChatGPT",
  // Loyalsoldier 规则集
  "DOMAIN,auth.openai.com,ai",
  "DOMAIN,api.oaistatsig.com,ai",
  "DOMAIN,auth.openai.com,ai",
  "RULE-SET,openai,ai"
  //"RULE-SET,applications,全局直连",
  //"RULE-SET,private,全局直连",
  //"RULE-SET,reject,广告过滤",
  //"RULE-SET,icloud,微软服务",
  //"RULE-SET,apple,苹果服务",
  //"RULE-SET,google,谷歌服务",
  //"RULE-SET,proxy,节点选择",
  //"RULE-SET,gfw,节点选择",
  //"RULE-SET,tld-not-cn,节点选择",
  //"RULE-SET,direct,全局直连",
  //"RULE-SET,lancidr,全局直连,no-resolve",
  //"RULE-SET,cncidr,全局直连,no-resolve",
  //"RULE-SET,telegramcidr,电报消息,no-resolve",
  // 其他规则
  //"GEOIP,LAN,全局直连,no-resolve",
  //"GEOIP,CN,全局直连,no-resolve",
  // "MATCH,漏网之鱼"
];


// 代理组通用配置
const groupBaseOption = {
  "interval": 300,
  "timeout": 3000,
  "url": "https://www.google.com/generate_204",
  "lazy": true,
  "max-failed-times": 3,
  "hidden": false
};



function main(config, profileName) {
  const oldRuleProviders = config['rule-providers']
  const oldRules = config['rules']
  const oldGroups = config['proxy-groups']
  const nonHkProxies = config['proxies'].map(proxy => proxy.name).filter(proxy => proxy.indexOf('HK')<0)
  const prependGroups = [
    {
      ...groupBaseOption,
      "name": "ai",
      "type": "select",
      "proxies": nonHkProxies,
      //"include-all": true,
      "icon": "https://fastly.jsdelivr.net/gh/clash-verge-rev/clash-verge-rev.github.io@main/docs/assets/icons/adjust.svg"
    }
  ]
  if (oldRuleProviders && length(oldRuleProviders) > 0) {
    config['rule-providers'] = ruleProviders.concat(oldRuleProviders)
  } else {
    config['rule-providers'] = ruleProviders
  }
  config['proxy-groups'] = prependGroups.concat(oldGroups)
  config['rules'] = rules.concat(oldRules)

  if (config.proxies) { 
    for (let i = 0; i < config.proxies.length; i++) { 
      config.proxies[i].udp = true; 
    } 
  } 
  // Enable UDP forwarding globally
  config.udp = true; 
  return config;
}


```



## QA

### 访问chatgpt时出现部分域名无法匹配clash rules
可能是使用了udp协议，可以检查clash connection连接情况，确认是否为udp链接，如果是可能是chrome开启了quic协议，在chrome://flags搜索quic，关闭quic协议支持



