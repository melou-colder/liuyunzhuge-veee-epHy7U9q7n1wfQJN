
作为“贴代码”力推的一个CRUD实践项目PasteTemplate,在对现有的3个项目进行实战后效果非常舒服！下面就针对PasteForm为啥我愿称为最佳CRUD做一些回答:
![image](https://img2024.cnblogs.com/blog/3266034/202409/3266034-20240930103013701-153487888.png)


### 哪里可以下载这个PasteForm的项目案例


目前“贴代码”对外使用PasteForm的项目有"贴Builder(PasteSpider)"和案例项目(PasteTemplate),其中案例项目你可以在
[https://gitee.com/pastecode/paste\-template](https://github.com) 获得


### PasteForm的CRUD模式有什么特色？


搞这个的起初是因为我有一个项目要在小程序上行实现大量的表单，思考了下，一大堆相似的代码，不是可以使用那个组件的方式实现么，那就需要传入模型，换位思考下，那么我们经常使用的CRUD是否也可以这么搞？
于是就有了这个利用多数据模型Dto和反射的原理实现的PasteForm，主要优点如下


1\.PasteForm输出的是一个思想，就是管理端的页面由后端控制，无论是安全性还是数据的表现


2\.前端编写一次后，后续都不用编写，如果你使用的是我的案例项目，那么你前端都可以不用编写，包括后端要新增表，修改字段等，前端都不再需要修改代码


3\.前端页面的超简洁性，无论你的后端是30个表还是100个表，对于前端来说都差不多大概4个页面搞定！当然了一些特殊的需求，还是需要编写下的！


4\.无语言限制，上面说得输出的是一个思想，本文的案例使用的是.netcore\+html的形式实现的，你也可以使用比如java\+vue的模式


5\.统一性，比如你30个表的图片上传，在表单中他们的样式都是一样的，因为他们其实使用的是同一个管理端页面


6\.后端统筹所有，比如字段的显示顺序，字段是否显示，字段的默认值等等等，都由后端对应的Dto来限制和控制，比如同样的新增，由于当前登陆用户的角色不同，你可以限制某一个字段张三必填，而李四可以是选填！


![image](https://img2024.cnblogs.com/blog/3266034/202409/3266034-20240930110449820-1951474989.png)


### 看到管理端的代码，感觉像JSP的?


整个PasteForm的原理就是后端把数据模型的属性，字段等返回给前端，前端基于后端返回的数据再渲染到UI上，为了便于编写所以采用了template的模式，其实不是JSP，是长得像而已！


### 我的项目拥有120个数据表，那是不是要编写很多的后端文件?


PasteForm原则上只有3个页面pasteform/index.html,pasteform/view.html,pasteform/detail.html，只是有一些特殊情况下，会添加一些页面，这个要看你项目的实际需求，其实无论多少个表，PasteForm的页面都只是那么几个，比如说权限的页面为pasteform/index.html?path\=roleInfo而用户的表为pasteform/index.html?path\=userInfo,你会发觉其实他们是同一个页面，只是参数path不一样而已，这个path就是WebApi的Service！
其实你完全可以一个页面都不写，直接使用我提供的案例项目PasteTemplate中的pasteform的文件!
![image](https://img2024.cnblogs.com/blog/3266034/202409/3266034-20240930110807384-155653188.png)


### 我看案例PasteTemplate项目是使用html,js,css编写的，可以使用vue编写管理端么？


上面提到的，PasteForm主要输出的是一个思想，也就是由后端的Dto控制前端的页面呈现和交互等，所以从WebApi中请求得到模板得属性字段等信息后，你可以使用其他语言实现管理端得页面得，不限原生，vue,angular等


### pasteform/index.html是干嘛用？


作为对应数据表得数据展示用，一般为表格table呈现，对应模型为XXXListDto，也包括了搜索区域，搜索区域由对应得InputQueryXXX的数据模型决定，一般只有page,size,word三个字段！当然了这个页面也包含了新增数据，编辑，详情，删除等的操作交互！


### pasteform/view.html是做什么用得?


新增数据，或者编辑数据的时候使用的都是这个页面，不过他们读取的数据模型不一样，比如权限这个表roleInfo,则有新增的时候为pasteform/view.html?path\=roleInfo，对应的数据模型为roleInfoAddDto,而在编辑的时候为pasteform/view.html?path\=roleInfo\&id\=3,对应的数据模型为roleInfoUpdateDto。页面的整体逻辑就是从WebApi中获得对应模型后，基于规则渲染到UI中，包括默认值，当前值等，提交的时候再从页面中的form读取到值提交给后端，所以说主要的控制还是在后端中！


### pasteform/detail.html是做什么用得?


有些时候我们的数据没办法在pasteform/index.html页面中显示完成，比如文章列表，主要是一些字段过长的，这个时候我们希望有一个页面显示详细内容，所以detail就是干这个用的，对应的数据模型为XXXDto！


### 我不会.netcore可以用其他语言写pasteform么


PasteForm输出的是一个思想，所以使用其他语言也是可以的，不过你得对着写一整套的就是了，比如你可以使用vue\+java的模式搞一套！后端核心代码案例



```
        /// 
        /// 读取AddDto的数据模型
        /// 
        /// 
        [HttpGet]
        [TypeFilter(typeof(RoleAttribute), Arguments = new object[] { "root", "root" })]
        public PasteBuilderHelper.VoloModelInfo ReadAddModel()
        {
            var _model = PasteBuilderHelper.ReadModelProperty(new RoleInfoAddDto());
            return _model;
        }

        /// 
        /// 读取UpdateDto的数据模型
        /// 
        /// 
        [HttpGet]
        [TypeFilter(typeof(RoleAttribute), Arguments = new object[] { "root", "root" })]
        public async Task ReadUpdateModel(int id)
        {

            var _query = from a in _dbContext.RoleInfo
                         join b in _dbContext.RoleInfo on a.FatherId equals b.Id into c
                         from rol in c.DefaultIfEmpty()
                         select new RoleInfoUpdateDto
                         {
                             Id = a.Id,
                             Desc = a.Desc,
                             FatherId = a.FatherId,
                             Icon = a.Icon,
                             IsEnable = a.IsEnable,
                             Model = a.Model,
                             Name = a.Name,
                             Path = a.Path,
                             Role = a.Role,
                             RoleType = a.RoleType,
                             Sort = a.Sort,
                             ExtendRole = rol != null ? new RoleShortModel
                             {
                                 Id = rol.Id,
                                 Model = rol.Model,
                                 Name = rol.Name,
                                 Path = rol.Path,
                                 Role = rol.Role,
                                 RoleType = rol.RoleType
                             } : null
                         };

            var _info = await _query.Where(x => x.Id == id).AsNoTracking().FirstOrDefaultAsync();
            if (_info == null || _info == default)
            {
                throw new PasteCodeException("查询的信息不存在，无法执行编辑操作！");
            }
            var _dataModel = PasteBuilderHelper.ReadModelProperty(_info);
            return _dataModel;
        }

        /// 
        /// 读取UpdateDto的数据模型
        /// 
        /// 
        [HttpGet]
        [TypeFilter(typeof(RoleAttribute), Arguments = new object[] { "root", "root" })]
        public async Task ReadDetailModel(int id)
        {

            var _query = from a in _dbContext.RoleInfo
                         join b in _dbContext.RoleInfo on a.FatherId equals b.Id into c
                         from rol in c.DefaultIfEmpty()
                         select new RoleInfoDto
                         {
                             Id = a.Id,
                             Desc = a.Desc,
                             FatherId = a.FatherId,
                             Icon = a.Icon,
                             IsEnable = a.IsEnable,
                             Model = a.Model,
                             Name = a.Name,
                             Path = a.Path,
                             Role = a.Role,
                             RoleType = a.RoleType,
                             SortStr = a.SortStr,
                             FatherStr = a.FatherStr,
                             Level = a.Level,
                             RootId = a.RootId,
                             Sort = a.Sort,
                             ExtendRole = rol != null ? new RoleShortModel
                             {
                                 Id = rol.Id,
                                 Model = rol.Model,
                                 Name = rol.Name,
                                 Path = rol.Path,
                                 Role = rol.Role,
                                 RoleType = rol.RoleType
                             } : null
                         };

            var _info = await _query.Where(x => x.Id == id).AsNoTracking().FirstOrDefaultAsync();
            if (_info == null || _info == default)
            {
                throw new PasteCodeException("查询的信息不存在，无法执行编辑操作！");
            }
            var _dataModel = PasteBuilderHelper.ReadModelProperty(_info);
            return _dataModel;
        }

        /// 
        /// 读取ListDto的数据模型
        /// 
        /// 
        [HttpGet]
        [TypeFilter(typeof(RoleAttribute), Arguments = new object[] { "root", "root" })]
        public PasteBuilderHelper.VoloModelInfo ReadListModel()
        {
            var _model = PasteBuilderHelper.ReadModelProperty(new RoleInfoListDto());
            var _query_model = PasteBuilderHelper.ReadModelProperty(new InputQueryRoleInfo());
            if (_query_model != null)
            {
                _model.QueryProperties = _query_model.Properties;
            }
            return _model;
        }


```

### PasteForm使用到的各个模型是干嘛用的，Dto


我最早接触Dto的时候是ABPvNext,说到这个Dto不得不说ObjectMapper,比如说权限表RoleInfo,则对应的有RoleInfoAddDto,RoleInfoUpdateDto,RoleInfoDto和RoleInfoListDto，当前实际开发中你可能还会引申出更多的Dto,比如我常用的RoleMenuDto,RoleAuthDto等，PasteForm中主要用到前面的4个，一般还会附带一个InputQueryRoleInfo!比如说新增的时候，用户提交的数据模型是RoleInfoAddDto,提交给API后，API处数据校验合法后再使用AutoMapper把RoleInfoAddDto转化成RoleInfo，然后写入到数据库！
XXXAddDto:用于新增的时候的数据模型，对应的是pasteform/view.html页面使用
XXXUpdateDto:用于数据编辑修改的数据模型，对应的是pasteform/view.html页面使用
XXXDto:这里我一般用于显示详情的时候的数据模型，也就是pasteform/detail.html的页面使用
XXXListDto:这里一般使用于数据表格展示的时候的数据模型，也就是pasteform/index.html的页面使用
InputQueryXXX:这里一般用于表格上方的搜索项的数据模型，也就是pasteform/index.html的搜索区域使用


### 如何上传图片?


图片分新增编辑和展示，展示的话这里就是pasteform/index.html和pasteform/detail.html页面中了，其实在编辑的时候pasteform/view.html页面上也有展示，只要给对应字段添加属性\[ColumnDataTypeAttribute("image","1","image","60\*60")]即可



```
        /// 
        /// 多图 回传的值是多个的，使用,隔开
        /// 
        [ColumnDataType("image", "3", "img", "60*60")]
        public string Img2 { get; set; }

        /// 
        /// 图片 回传的使用string[]的模式
        /// 
        [ColumnDataType("image", "3", "img", "60*60")]
        public string[] Img3 { get; set; }

```

### 表格中如何让数据左靠



```
        ///
        ///文本区域 模拟文本区域的输入
        ///
        [ColumnDataType("class","fleft")]
        public string Desc { get; set; }

```

### 表格中如何自定义显示



```
        ///
        ///单选 一般表示状态，内定的，有点像Enum,关于Enum后续会支持
        ///
        [ColumnDataType("html", "{{:=item.dateType}}-{{:=item.gradeId}}")]
        public int DateType { get; set; }

```

### 表格中如何显示按钮



```
        /// 
        /// 普通菜单
        /// 
        [ColumnDataType("menu", "菜单一", "open_window('查阅用户带参','./index.html?path=userInfo&xxid={{:=item.id}}');", "Hui-iconfont-menu")]
        public string Menu2 { get; set; }

```

### 表格中如何显示条件按钮


有些时候我们需要基于当前行数据进行判断，是否显示某一个按钮，则有



```
        /// 
        /// 普通条件菜单
        /// 
        [ColumnDataType("ifmenu", "item.age==7", "[javascript:;\" onclick=\"open_window(`111`,`./index.html?path=userInfo&goid={{:=item.id}}`)\">条件1](\"</span):[veee加速器](https://liuyunzhuge.com)", "")]
        public string Menu3 { get; set; }

```

### 按钮太多，如何使用按钮盒子



```
        /// 
        /// 菜单盒子菜单
        /// 
        [ColumnDataType("menu", "菜单二", "open_window('查阅用户带参','./index.html?path=userInfo&xxid={{:=item.id}}');", "Hui-iconfont-menu", "box")]
        public string Menu5 { get; set; }

        /// 
        /// 菜单盒子中的条件菜单
        /// 
        [ColumnDataType("ifmenu", "item.age==8", "[javascript:;\" onclick=\"open_window(`222`,`./index.html?path=userInfo&goid={{:=item.id}}`)\">条件2](\"</span)", "box")]
        public string Menu4 { get; set; }

```

### 按照排序查下，某些字段支持升序降序


前端只是基于这个属性，在查下的时候会回传orderby字段给后端，后续需要基于回传的这个字段进行orderby查询



```

        ///
        ///排序
        ///
        [ColumnDataType("orderby", "Sort","Sort desc")]
        public int Sort { get; set; }

```

\-\-\- 未完待续，下期继续 \-\-\-


