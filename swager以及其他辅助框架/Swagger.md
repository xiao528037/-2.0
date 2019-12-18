## Swagger

## 概述

Swagger是一个围绕OpenAPI对反构建的开源工具，可帮助设计，构建，记录和使用RESTAPI。简单来说，他的出现就是为了方便进行测试后台的RESTful形式的接口，实现动态的更新，当我们在后台的接口修改后，swagger可以实现自动的更新，而不需要认为的维护这个接口进行测试。



swagger通过注解表明该接口会生成文档，包括接口，请求方法，参数，返回信息等。

- @Api:修饰整个类，修饰Controller
- @ApiOperation:描述一个类的一个方法，或者说一个接口
- @ApiParam:单个参数描述
- @ApiModel:用对象来接受参数
- @ApiProperty:用对象接受参数是，描述对象的一个字段
- @ApiResponse:HTTP响应其中一个描述
- @ApiResponses:HTTP相应整体描述
- @ApiIgnore:使用该注解忽略这个API
- @ApiError：发生错误返回的信息
- @ApiParamImplicitL:一个请求参数
- @ApiParamsImplicit多个请求参数