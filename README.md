Name
====
openwaf_reqstat是[openwaf](https://github.com/titansec/openwaf)的统计模块，对访问信息、安全信息、转发信息等进行统计

Table of Contents
=================
* [Name](#name)
* [Synopsis](#synopsis)
* [Statistical information](#statistical-information)
* [Prerequisite modules](#prerequisite-modules)
* [Configuration Directives](#configuration-directives)
* [API](#api)

Synopsis
========
```lua
    -- app/openwaf_init.lua
    local twaf_reqstat_m = require "lib.twaf.twaf_reqstat"
    twaf_reqstat = twaf_reqstat_m:new(config, policy_uuids)
    
    -- app/api.lua
    api["get"]["reqstat"] = function() twaf_reqstat:reqstat_show_handler(policy_uuid) end
    api["delete"]["reqstat"] = function() twaf_reqstat:reqstat_clear() end
    
    -- app/openwaf_log.lua
    twaf_reqstat:reqstat_log_handler(events, policy_uuid)
```
[Back to TOC](#table-of-contents)

Statistical information
=======================
```txt
{
	"main": {                                 -- 全局统计信息
		"nginx_version": "1.7.10",            -- 引擎版本号
        "loadsec": 1438843635,                -- 引擎启动时间
        "resetsec": 1441610618,               -- 引擎重置时间（http://path/stat/clear触发，0表示自上次启动引擎，未重置过）
   		"connection": {                       -- 总连接信息
	        "active": "1",                    -- 活动连接数（即正在处理的）
		    "writing": "1",                   -- 空闲连接（开启keep-alive）
			"accepted": "2",                  -- 处理的总连接数
			"requests": "2",                  -- 处理的总请求数
			"handled": "2",                   -- 成功创建连接数（与accepted相等，证明没有创建失败的连接）
   			"waiting": "0",                   -- 返回给客户端的Header信息数
			"reading": "0"                    -- 读取到客户端的Header信息数
		},
		"access": {                           -- 访问信息
            "addr_total":1,                   -- 独立IP访问总数（暂不提供此值）
			"req_total": 2,                   -- 请求总数
            "conn_total":2,                   -- 处理客户端与WAF之间的总连接数
            "attack_total":1,                 -- 检测到的攻击总数
			"bytes_in": 680,                  -- 接收到的字节数
			"bytes_out": 7445,                -- 向客户端发送的字节数
			"1xx": 0,                         -- 响应为1xx的请求数
			"2xx": 2,                         -- 响应为2xx的请求数
			"3xx": 0,                         -- 响应为3xx的请求数
			"4xx": 0,                         -- 响应为4xx的请求数
			"5xx": 0                          -- 响应为5xx的请求数
		},
		"safe": {                             -- 安全防护模块信息
			"twaf_limit_conn_requests": 0,    -- 触发cc攻击的请求数
            "twaf_limit_conn_times":0,        -- 进入cc防护状态的次数
			"twaf_acl": 0,                    -- 触发acl模块的请求数
			"twaf_anti_robot": 0,             -- 触发人机识别模块的请求数
			"twaf_cookie_guard": 0,           -- 触发cookie保护模块的请求数
			"twaf_anti_hotlink": 0,           -- 触发盗链攻击的请求数
            "twaf_anti_mal_crawler": 0,       -- 触发恶意爬虫检测模块的请求数
            "twaf_libinjection_sqli": 0,      -- 触发SQLi攻击的请求数
            "twaf_libinjection_xss": 0,       -- 触发xss攻击的请求数
            "mod_security":0                  -- 匹配中mod_security规则的请求数
		},
		"upstream": {                         -- 转发信息
		    "req_total":1,                    -- 转发请求总数
			"bytes_in":135,                   -- 接收到的字节数
			"bytes_out":2190,                 -- 向客户端发送的字节数
			"1xx":0,                          -- 响应为1xx的请求数
			"2xx":1,                          -- 响应为2xx的请求数
			"3xx":0,                          -- 响应为3xx的请求数
			"4xx":0,                          -- 响应为4xx的请求数
			"400":0,                          -- 响应为400的请求数
			"401":0,                          -- 响应为401的请求数
			"403":0,                          -- 响应为403的请求数
			"404":0,                          -- 响应为404的请求数
			"405":0,                          -- 响应为405的请求数
			"406":0,                          -- 响应为406的请求数
			"407":0,                          -- 响应为407的请求数
			"408":0,                          -- 响应为408的请求数
			"409":0,                          -- 响应为409的请求数
			"410":0,                          -- 响应为410的请求数
			"411":0,                          -- 响应为411的请求数
			"412":0,                          -- 响应为412的请求数
			"413":0,                          -- 响应为413的请求数
			"414":0,                          -- 响应为414的请求数
			"415":0,                          -- 响应为415的请求数
			"416":0,                          -- 响应为416的请求数
			"417":0,                          -- 响应为417的请求数
			"5xx":0                           -- 响应为5xx的请求数
			"500":0                           -- 响应为500的请求数
			"501":0                           -- 响应为501的请求数
			"502":0                           -- 响应为502的请求数
			"503":0                           -- 响应为503的请求数
			"504":0                           -- 响应为504的请求数
			"505":0                           -- 响应为505的请求数
			"507":0                           -- 响应为507的请求数
		}
	},
	"policy":{                                -- 某策略的统计信息
	    "access":{
	         --统计内容同"main":"access"
	    },
	    "safe":{
	         --统计内容同"main":"safe"
	    },
	    "upstream":{
	         --统计内容同"main":"upstream"
	    }
	}
}
```
[Back to TOC](#table-of-contents)

Prerequisite modules
====================
"connection"中的"active","reading","writing","waiting"需要ngx_http_stub_status_module模块支持

"connection"中的"accepted","handled","requests"及"access"中的"bytes_in"需要ngx_http_openwaf_variable_module模块支持

* [nginx_http_stub_status_module变量](http://nginx.org/en/docs/http/ngx_http_stub_status_module.html#variables)
* [ngx_http_openwaf_variable_module变量](https://github.com/miracleqi/ngx_http_openwaf_variable_module/blob/master/README_CN.md#variables)

[Back to TOC](#table-of-contents)

Configuration Directives
========================
```json
    "twaf_reqstat": {
        "state":true,
        "safe_state":true,
        "access_state":true,
        "upstream_state":true,
        "shared_dict_name":"twaf_reqshm",
        "content_type":"JSON"
    }
```
[Back to TOC](#table-of-contents)

###state
**syntax:** *state true|false|$dynamic_state*

**default:** *true*

**context:** *twaf_reqstat*

统计模块开关，支持动态开关，默认开启

###access_state
**syntax:** *access_state true|false|$dynamic_state*

**default:** *true*

**context:** *twaf_reqstat*

访问信息统计开关，支持动态开关，默认开启

###safe_state
**syntax:** *safe_state true|false|$dynamic_state*

**default:** *true*

**context:** *twaf_reqstat*

安全信息统计开关，支持动态开关，默认开启

###upstream_state
**syntax:** *upstream_state true|false|$dynamic_state*

**default:** *true*

**context:** *twaf_reqstat*

转发信息统计开关，支持动态开关，默认开启

###shared_dict_name
**syntax:** *shared_dict_name string*

**default:** *openwaf_reqshm*

**context:** *twaf_reqstat*

指定shared_dict名称，在这之前需在nginx配置文件中配置[lua_shared_dict](https://github.com/openresty/lua-nginx-module#lua_shared_dict) <name> <size>

默认shared_dict名称为openwaf_reqshm

###content_type
**syntax:** *content_type JSON|INFLUXDB*

**default:** *JSON*

**context:** *twaf_reqstat*

指定统计信息输出格式，目前支持JSON和INFLUXDB两种格式

[Back to TOC](#table-of-contents)

API
===
* [new](#new)
* [reqstat_log_handler](#reqstat_log_handler)
* [reqstat_show_handler](#reqstat_show_handler)
* [get_reqstat_main_info](#get_reqstat_main_info)
* [get_reqstat_uuid_info](#get_reqstat_uuid_info)
* [reqstat_clear](#reqstat_clear)

[Back to TOC](#table-of-contents)

new
---
**syntax:** *reqstat = new(self, config, policy_uuids)*

实例化统计对象

reqstat_log_handler
-------------------
**syntax:** *reqstat:reqstat_log_handler(events, policy_uuid)*

记录

reqstat_show_handler
--------------------
**syntax:** *reqstat:reqstat_show_handler()*

通过请求参数获取统计信息

get_reqstat_main_info
---------------------
**syntax:** *reqstat:get_reqstat_main_info()*

获取全局统计信息

get_reqstat_uuid_info
---------------------
**syntax:** *reqstat:get_reqstat_uuid_info(uuids)*

获取策略统计信息

reqstat_clear
-------------
**syntax:** *reqstat:reqstat_clear()*

清空统计信息

[Back to TOC](#table-of-contents)
