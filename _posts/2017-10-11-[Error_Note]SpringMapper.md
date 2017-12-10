---
title: "[Error] - Mapper"
description: "ErrorNote - Spirng VO 와 database의 column의  이름이 맞지 않을때,"
categories: 
 Error Mysql Spring 
tags: mapper 
date: 2017-10-11 
comments : false
---

# Error 

* Spring - mapper Test

## Exception

* value 가 계속 Null 로 들어오는 경우


## 해결방법

* column 명과 VO의 이름을 맞추기 위해서 Mapper에서 alias 로 직접 다시 VO의 이름으로 맞춰준다

**EX**


* BoardVO

```java

public class BoardVO {
    private Integer boardId;
    private String boardTitle;
    private String boardContent;
    private String boardWriter;
    private Date boardRegdate;
    private int boardViewcnt;
    private String boardCategoryName;
    private int boardLikecnt;
    }


``` 

* Mysql column

>  board_id         
  Board_title      
  board_writer     
  board_content    
  board_regdate    
  board_update     
  Board_viewcont   
  board_category_id
  board_likecnts   


* mapper
    

```xml

 <select id="selectAll" resultType="BoardVO">
        SELECT board_id boardId,
               board_title boardTitle,
               board_content boardContent,
               (SELECT user_name FROM tbl_user WHERE tbl_user.user_id = tbl_board.board_writer) boardWriter,
               board_regdate boardRegdate,
               board_viewcnt boardViewcnt,
               (SELECT category_name from tbl_board_category WHERE tbl_board_category.category_id = tbl_board.board_category_id) boardCategoryName,
               board_likecnt boardLikecnt
        FROM tbl_board WHERE tbl_board.board_id > 0
        ORDER  BY tbl_board.board_id DESC , board_regdate DESC
    </select>

```