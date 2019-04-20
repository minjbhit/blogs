# :sparkling_heart:项目介绍 #
在慕课网上发现了一个JavaWeb项目，内容讲的是高并发秒杀，觉得挺有意思的，就进去学习了一番。

记录在该项目中学到了什么玩意..

该项目源码对应的gitHub地址（由观看其视频的人编写，并非视频源代码）：https://github.com/codingXiaxw/seckill

我结合其资料和观看视频的时候整理出从该项目学到了什么...
## 项目Dao层 ##
### 日志记录工具： ###
    <!--1.日志 java日志有:slf4j,log4j,logback,common-logging
        slf4j:是规范/接口
        日志实现:log4j,logback,common-logging
        使用:slf4j+logback
    -->
## Mybatis之前没注意到的配置属性： ##
**使用jdbc的getGeneratekeys获取自增主键值**，这个属性还是挺有用的。

        <?xml version="1.0" encoding="UTF-8" ?>
        <!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
        <configuration>
            <!--配置全局属性-->
            <settings>
                <!--使用jdbc的getGeneratekeys获取自增主键值-->
                <setting name="useGeneratedKeys" value="true"/>
                <!--使用列别名替换列名　　默认值为true
                select name as title(实体中的属性名是title) form table;
                开启后mybatis会自动帮我们把表中name的值赋到对应实体的title属性中
                -->
                <setting name="useColumnLabel" value="true"/>

                <!--开启驼峰命名转换Table:create_time到 Entity(createTime)-->
                <setting name="mapUnderscoreToCamelCase" value="true"/>
            </settings>

        </configuration>
Mybatis返回的对象如果有关联字段，除了使用resultMap还有下面这种方式（虽然我还是觉得resultMap会方便一点）

            <select id="queryByIdWithSeckill" resultType="SuccessKilled">
                <!--根据seckillId查询SuccessKilled对象，并携带Seckill对象-->
                <!--如何告诉mybatis把结果映射到SuccessKill属性同时映射到Seckill属性-->
                <!--可以自由控制SQL语句-->
                SELECT
                    sk.seckill_id,
                    sk.user_phone,
                    sk.create_time,
                    sk.state,
                    s.seckill_id "seckill.seckill_id",
                    s.name "seckill.name",
                    s.number "seckill",
                    s.start_time "seckill.start_time",
                    s.end_time "seckill.end_time",
                    s.create_time "seckill.create_time"
                FROM success_killed sk
                INNER JOIN seckill s ON sk.seckill_id=s.seckill_id
                WHERE sk.seckill_id=#{seckillId}
                AND sk.user_phone=#{userPhone}
            </select>
数据库连接池可能用到的属性：

         <!--c3p0私有属性-->
        <property name="maxPoolSize" value="30"/>
        <property name="minPoolSize" value="10"/>
        <!--关闭连接后不自动commit-->
        <property name="autoCommitOnClose" value="false"/>

        <!--获取连接超时时间-->
        <property name="checkoutTimeout" value="1000"/>
        <!--当获取连接失败重试次数-->
        <property name="acquireRetryAttempts" value="2"/>
