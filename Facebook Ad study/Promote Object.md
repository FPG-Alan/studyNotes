# Promoted Object (推广对象)

Promoted Object 是指Ad Set正在推广的对象, 比如说一个目标为"Page like"的Campaign中, Page就是推广对象. 这是一种泛化的指定一组与广告目标相关的对象的方式.

总得来说, campaign的目标(objective)和ad set的promoted_object两者结合, 应该可以推导出

>这个adset/campaign到底是个啥, 像是 "这个campaign是要为我的Page X 获得更多的赞", 或者 "这个ad set是要推广我的App Y"

当你在一个已经设置了目标的Campaign内创建一个Ad set时, promoted_object字段是必要的.


### 推广对象认证
当你在推广对象中指定 application_id 和 object_store_url 两个字段时:

- object_store_url 字段必须和对应App关联, 你可以在你的应用程序设置页进行配置(这里说的App是Facbbook App.)
- ad set 针对移动系统/设备目标设置必须匹配指定的App的平台.
- Creative 必须链接到指定的 object_store_url
- application_id 和 object_store_url 必须同时存在
- 当指定一个 page_id 时, creative 必须推广同样的 page_id
- 你必须拥有你指定的 page_id, application_id 和 pixel_id 的权限, 否则无法进行推广
- 如果提供了一个 pixel_id, 你必须提供想要优化的 custom_event_type 

### 推广对象限制
如果你使用推广对象, 请注意以下几点:

1. promoted_object 大部分时间是不可变的, 大部分情况下, 它在生成的时候设置, 然后就不能被改变. 如果需要推广不同的对象, 你需要重新创建一个ad set, (极少可以改变的: 1. 初始化的时候没有指定 application_id 或 product_catalog_id 的值, 在之后提供. 2. 更新 pixel_id, pixel_rule 或 custom_event_type 的值.)
2. 已经存在的, 并且没有设置推广对象的ad set是不允许重新设置推广对象的, 除非是上面说的例外条件.
3. 当一个推广对象被设置, conversion_specs 字段会根据指定的目标(objectives) **自动推断**, 你不需要手动设置 objectives, 并且任何传入的值都会被忽略
4. 没有设置promote_object的旧版广告集中的现有广告可以更新所有广告字段。(Existing ads within a legacy ad set without a promoted_object set can update all ad fields.)



