# 报错 required Long parameter 'userPk' is not present json
```java
    @ApiOperation(value = "查看所属仓库-分页查询", notes = "查看所属仓库-分页查询")
    @PostMapping(value = "/queryWareHousePage")
    public Result queryWareHouse(
            @ApiParam(value = "{\"filters\": [],\"paginate\":{\"pageNumber\": 1,\"pageSize\": 50},\"userPk\": 1L}")
            @RequestBody JSONObject json
    ) {
        try {
            Number num = (Number)json.get("userPk");
            if (num == null) {
                return Result.fail("用户编码不能为空");
            }
            Long userPk = num.longValue();
            Page<Record> record = scsPurchaserService.queryWareHousePage(myBatisKit.getPageByFilterJson(json), userPk);
            return Result.success(record);
        } catch (Exception e) {
            e.printStackTrace();
            return Result.fail(e.getMessage());
        }
    }
```

- fastjson 和 jackson 默认情况下整数类型优先选取 Integer，超过 Integer 范围再选择 Long

