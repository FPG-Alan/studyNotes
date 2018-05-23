# 4. 提供 Ad creative
使用 AdCreative 来设计你的广告格式. 在 AdCreative 中提供什么信息取决于广告的目标([objective](1)), 一般包括下列属性:

- 一个或多个图片或视频(已经上传了的)
- 标题, 描述等
- 将你的广告链接到一个 Facebook page
- 添加一个 "Call To Action" 按钮 (CTA)

此外, 基于你的广告目标, 你还需要提供进一步的字段. 比如, 一个 IOS app 的广告需要一个应用商店的地址. 而一个目标为 "website clicks" 的广告, 应该提供对应的网站地址.

#### 实例
1. 首先, 使用一张图片构建一个 AdImage
    ```python
    from facebook_business.adobjects.adimage import AdImage

    image = AdImage(parent_id='act_<AD_ACCOUNT_ID>')
    image[AdImage.Field.filename] = '<IMAGE_PATH>'
    image.remote_create()

    # Output image Hash
    print(image[AdImage.Field.hash])
    ```

2. 然后用这张图片(hash)创建一个 AdCreative
    ```python
    from facebook_business.adobjects.adcreative import AdCreative
    from facebook_business.adobjects.adcreativelinkdata import AdCreativeLinkData
    from facebook_business.adobjects.adcreativeobjectstoryspec import AdCreativeObjectStorySpec

    link_data = AdCreativeLinkData()
    link_data[AdCreativeLinkData.Field.message] = 'try it out'
    link_data[AdCreativeLinkData.Field.link] = '<LINK>'
    link_data[AdCreativeLinkData.Field.image_hash] = '<IMAGE_HASH>'

    object_story_spec = AdCreativeObjectStorySpec()
    object_story_spec[AdCreativeObjectStorySpec.Field.page_id] = <PAGE_ID>
    object_story_spec[AdCreativeObjectStorySpec.Field.link_data] = link_data

    creative = AdCreative(parent_id='act_<AD_ACCOUNT_ID>')
    creative[AdCreative.Field.name] = 'AdCreative for Link Ad'
    creative[AdCreative.Field.object_story_spec] = object_story_spec
    creative.remote_create()

    print(creative)
    ```
    注意这里需要link到一个page, 而这个page需要在published状态


# 5. 交付投放
现在, 可以使用`Ad`来创建一个Facebook广告. 你需要将一个`AdCreative`和`AdSet`关联至`Ad`来产生一个新的对象, 这个对象就指代你的广告. 设置`Ad`的`status`属性为`paused`来避免立即产生费用.



