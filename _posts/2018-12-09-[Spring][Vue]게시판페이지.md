---
title: "[Spring] [Vue] 게시판 페이지로 데이터 넘기기"
description: "Spring controller 에서 Vue 객체로 데이터 넘기기 "
categories: Spring Vue
tags: Spring Vue board json 
date: 2017-12-09
comments : true
---

* 기본적으로 Spring 에서는 데이터를 객체 형태로 생성을 하고 그상태에서 사용을한다.

* 하지만 Vue 에서는 데이터 바인딩을 기본적으로 Json 파일 형식으로 사용을 한다.

* 그래서 VO 데이터를 JSON 형태로 가공해서 controller 에서 보내주어야 Vue 객체에서 사용이 가능하다.

# dependency 추가

```xml

<dependency>
	<groupId>com.googlecode.json-simple</groupId>
	<artifactId>json-simple</artifactId>
	<version>1.1.1</version>
</dependency>

```
* 기본적으로 JAVA 에서 json 객체를 만들기 위해서는 `json-simple`이 필요하다.

# controller 

```java

@Controller
@RequestMapping("/freeBoard/*")
public class BoardController {

  @RequestMapping(value = "/list", method = RequestMethod.GET, headers="Accept=application/json")
    public void listAll(Model model) throws Exception {

        logger.info("show all list......................");

        List<BoardVO> boardVOS = service.listAllBoard();


        JSONObject listObj = new JSONObject();
        JSONArray list = new JSONArray();
        for(int i = 0 ; i < boardVOS.size() ; i++) {

            JSONObject vo = new JSONObject();

            vo.put("boardId",boardVOS.get(i).getBoardId());
            vo.put("boardTitle",boardVOS.get(i).getBoardTitle());
            vo.put("boardWriter",boardVOS.get(i).getBoardWriter());
            vo.put("boardViewcnt",boardVOS.get(i).getBoardViewcnt());
            vo.put("boardLikecnt",boardVOS.get(i).getBoardLikecnt());

            list.add(i,vo);
        }
        listObj.put("BoardVO",list);


        model.addAttribute("list",listObj);
    }
}

```

* controller 에서 `model`객체에 list 를 담아서 보낸다.

* 현재 JSON 객체의 형태는 아래와 같이 되어있다.

>  JSONObject > JSONArray > JSONObject

* boardVO 의 데이터가 JSONArray 에 저장되어 있고, Array를 다시한번 JSONObject로 감싸고 있는 형태로 보낸다.

# View


```jsp

<script>
 var boardVO = ${list};
</script>

```

* 위에 결과처럼 받아보면 list객체가 깔끔하게 JSON 형태로 되어있는것을 확인 할 수 있다.



