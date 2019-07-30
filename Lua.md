# Lua

### 注释
单行注释

```lua
--   这是一行注释
```

多行注释

```lua
--[
这是多行注释
这是多行注释
这是多行注释
--]
```

### for 循环

```lua
for var=exp1,exp2,exp3 do  
    <执行体>  
end 
--[
var 从 exp1 变化到 exp2，变化的步长是 exp3，同时执行一次 "执行体"
如果 exp3 省略，那么步长就为 1
--]
```

### table 表

```lua
local table1 = {}    --空的表
local table2 = {"A", "B", "C", "D"}   --创建时直接初始化
local table3 = {[1] = "A", [2] = "B"} --直接给对应下标赋值
```

