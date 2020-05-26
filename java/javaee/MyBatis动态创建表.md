# MyBatis动态创建表

## Mapper.java
```java
import com.xx.xx.DemoData;
import org.apache.ibatis.annotations.Param;

import java.util.List;

public interface DemoDataMapper {

    List<DemoData> getAll(@Param("tableName") String tableName);

    int existTable(@Param("tableName") String tableName);

    void createTable(@Param("tableName") String tableName);

    void dropTable(@Param("tableName") String tableName);

    void insertBatch(@Param("tableName") String tableName, @Param("dataList") List<DemoData> dataList);

}

```

## Mapper.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.xx.xx.mapper.DemoDataMapper">
    <resultMap id="BaseResultMap" type="com.xx.xx.DemoData">
        <result column="id" jdbcType="VARCHAR" property="id"/>
        <result column="name" jdbcType="VARCHAR" property="name"/>
    </resultMap>

    <select id="getAll" resultMap="BaseResultMap">
		SELECT  id, name FROM ${tableName}
	</select>

    <select id="existTable" resultType="Integer">
        SELECT COUNT (*) FROM information_schema.TABLES WHERE TABLE_NAME = #{tableName}
    </select>

    <update id="createTable">
        CREATE TABLE ${tableName} (
          id VARCHAR(32) NOT NULL,
          name VARCHAR(32) NOT NULL
        )
    </update>

    <update id="dropTable">
        DROP TABLE IF EXISTS ${tableName}
    </update>

    <insert id="insertBatch">
        INSERT INTO ${tableName}
        (id, name)
        VALUES
        <foreach collection="dataList" item="item" index="index" separator=",">
            (
            #{item.id},
            #{item.name}
            )
        </foreach>
    </insert>

</mapper>
```

> 注：@Param指定的参数名和mapper.xml中对应，解决多个ParameterType的问题