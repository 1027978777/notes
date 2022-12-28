```java
package com.rkinf.poms.api.utils;

import com.google.common.base.Splitter;
import com.google.common.collect.Lists;
import lombok.Data;
import org.apache.commons.lang3.StringUtils;

import java.util.ArrayList;

/**
 * 根据表格生成建表语句
 * 
 * @author ll
 * @date 2022/12/06 14:07
 **/
@Data
public class CreatTable {
    private static final String VARCHAR = "       text,\n";
    private static final String JSON = "      json[],\n";
    private static final String JSONOBJ = "      json,\n";

    private static final String TABLE_NAME = "air_pollutant_organized_emission";

    private static final String TABLE_CONTENT =
            "3.8.\t企业申报-大气污染物排放信息-有组织排放信息（获取大气污染物排放信息-有组织排放信息）";

    private static final String KEY =

                                 "dataid\n" +
                                 "othercontentzy\n" +
                                 "othercontentqt\n" +
                                 "othercontentqc\n" +
                                 "groupinfo\n" +
                                 "specialtimeprocess\n" +
                                 "gasgrouptableoneList\n" +
                                 "gasgrouptabletwoList\n" +
                                 "gasgroupcounttableoneList\n" +
                                 "gasgroupcounttabletwoList\n" +
                                 "gasgroupcounttablethreeList\n" +
                                 "tsList\n" +
                                 "cfList\n"

           ;

    private static final String description =

       "数据主键，此字段非常重要，使用此字段查询表单的具体内容\n" +
               "主要排放口备注信息\n" +
               "一般排放口备注信息\n" +
               "全厂有组织排放口总计备注信息\n" +
               "申请年排放量限值计算过程\n" +
               "申请特殊时段许可排放量限值计算过程\n" +
               "1.主要排放口list集合\n" +
               "2.一般排放口list集合\n"+
               "1.主要排放口合计list集合\n" +
               "2.一般排放口合计list集合\n" +
               "3.全场有组织排放总计list集合\n"+
               "申请特殊时段许可排放量限值\n"+
               "错峰生产时段月许可排放量限值\n"
            ;

    public static void main(String[] args) {
        Iterable<String> ke = Splitter.on("\n").trimResults().omitEmptyStrings().split(KEY);
        ArrayList<String> ke1 = Lists.newArrayList(ke);
        String table = "create table xk.t_" + TABLE_NAME + "\n" + "(\n" + "    id      varchar(64) not null\n"
            + "        constraint t_" + TABLE_NAME + "_pkey\n" + "            primary key,\n";

        // 建表语句
        for (String key : ke) {
            if (key.contains("List") || key.contains("list")|| key.contains("applyMonth")) {
                table = table + key + JSON;
            } else if(key.contains("Map")||key.contains("industrialTotal")) {
                table = table + key + JSONOBJ;
            }
            else {
                table = table + key + VARCHAR;
            }
        }
        table = StringUtils.reverse(table).replaceFirst(",", "    ;)\n");
        table = StringUtils.reverse(table);
        System.out.println(table);
        System.out.println("key:" + ke1.size());
        // 注释语句
        Iterable<String> de = Splitter.on("\n").trimResults().omitEmptyStrings().split(description);
        ArrayList<String> de1 = Lists.newArrayList(de);
        System.out.println("description:" + de1.size());
        String column = "comment on column xk.t_" + TABLE_NAME + ".";
        String columnName = "comment on table xk.t_" + TABLE_NAME + " is" + " '" + TABLE_CONTENT + "';\n";
        for (int i = 0; i < de1.size(); i++) {
            columnName += column + ke1.get(i) + " is " + " '" + de1.get(i) + "';\n";
        }

        System.out.println(columnName);



    }

}

```

