---
title: "[Spring] 게시판 crud"
description: "Spring 에서 게시판 CRUD 를 구성 한다."
categories: Spring Mysql CRUD
tags: Spring Mysql CRUD Junit TestCode
date: 2017-12-03
comments : true
---

# VO 

* 테이블과 대치되는 class 로, 테이블을 객체 형태로 관리하기 위해 VO 를 생성한다.

```java

public class BoardVO {
    private Integer boardId;
    private String boardTitle;
    private String boardContent;
    private String boardWriter;
    private Date boardRegdate;
    private int boardViewcnt;
    private String boardCategoryName;
    
    // 이하 getter setter tostring 생략
   
    }

```

# DAO 

* 실질적인 기능을 interface로 명세해준다.

```java

public interface BoardDAO {

    public void createBoard(BoardVO vo) throws Exception;

    public BoardVO readBoard(int bno) throws  Exception;

    public List<BoardVO> readAllBoard() throws  Exception;

    public void updateBoard(BoardVO vo)throws Exception;

    public void deleteBoard(int bno)throws Exception;

    public List<BoardVO> readCategoryBoard(String categoryname) throws Exception;



```

# mapper

* 실질적으로 sql 문이 들어갈 mapper 를 작성한다. 

```xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="mapper.boardMapper">
    <insert id="insert">
        INSERT INTO tbl_board (board_title, board_content, board_writer,board_category_id)
          VALUES (#{boardTitle},#{boardContent},(SELECT user_id FROM tbl_user WHERE user_name = #{boardWriter}),(SELECT category_id FROM tbl_board_category WHERE category_name = #{boardCategoryName}))
    </insert>

    <select id="selectAll" resultType="BoardVO">
        SELECT board_id boardId, board_title boardTitle, board_content boardContent, (SELECT user_name FROM tbl_user WHERE tbl_user.user_id = tbl_board.board_writer) boardWriter, board_regdate boardRegdate, board_viewcnt boardViewcnt, (SELECT category_name from tbl_board_category WHERE tbl_board_category.category_id = tbl_board.board_category_id) boardCategoryName
        FROM tbl_board WHERE tbl_board.board_id > 0
        ORDER  BY tbl_board.board_id DESC , board_regdate DESC
    </select>

    <select id="selectCategoryBoard" resultType="BoardVO">
         SELECT board_id boardId, board_title boardTitle, board_content boardContent, (SELECT user_name FROM tbl_user WHERE tbl_user.user_id = tbl_board.board_writer) boardWriter, board_regdate boardRegdate, board_viewcnt boardViewcnt
        FROM tbl_board WHERE tbl_board.board_id > 0 AND board_category_id = (SELECT board_category_id FROM tbl_board_category where category_name = #{categoryName})
        ORDER  BY tbl_board.board_id DESC , board_regdate DESC
    </select>

    <select id="select" resultType="BoardVO">
        SELECT  board_id boardId, board_title boardTitle, board_content boardContent, (SELECT user_name FROM tbl_user WHERE tbl_user.user_id = tbl_board.board_writer) boardWriter, board_regdate boardRegdate, board_viewcnt boardViewcnt, board_category_id  boardCategoryId FROM tbl_board WHERE board_id = #{boardId}
    </select>

    <update id="update">
        update tbl_board set board_title =#{boardTitle}, board_content =#{boardContent}
        where tbl_board.board_id = #{boardId}
    </update>

    <delete id="delete">
        delete from tbl_board where board_id = #{boardId}
    </delete>

</mapper>


```

# DAOImpl 

* Mapper를 사용해서 crud를 구현한다.

```java

@Repository
public class BoardDAOImpl implements BoardDAO {

    @Inject
    private SqlSession session;

    final String namespace = "mapper.boardMapper";

    @Override
    public void createBoard(BoardVO vo) throws Exception {

        session.insert(namespace+".insert",vo);
    }

    @Override
    public BoardVO readBoard(int bno) throws Exception {
        return session.selectOne(namespace+".select",bno);
    }

    @Override
    public List<BoardVO> readAllBoard() throws Exception {

        return session.selectList(namespace+".selectAll");
    }

    @Override
    public void updateBoard(BoardVO vo) throws Exception {
        session.update(namespace+".update",vo);
    }

    @Override
    public void deleteBoard(int bno) throws Exception {
        session.delete(namespace+".delete",bno);

    }

    @Override
    public List<BoardVO> readCategoryBoard(String categoryname)throws Exception {
        return session.selectList(namespace+".selectCategoryBoard" , categoryname);
    }

}

```

# DAO Test

* 지금까지 만든 기능을 테스트한다.

* 테스트는 crud create, read, update, delete 모두 수행하는 테스트 케이스를 만든다.

* 실제적으로 사용하는 상황을 만들어서 서술하면서 쓰면 좀더 효과적이다.

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"file:src/main/webapp/WEB-INF/**/applicationContext.xml"})
public class BoardTest {

    @Inject
    private BoardDAO dao;

    @Inject
    private BoardService boardService;

    @Before
    public void BeforeTest() throws Exception{
        System.out.println("Before create Board");
        BoardVO vo = new BoardVO();

        vo.setBoardContent("첫번째 본문");
        vo.setBoardTitle("첫번째 제목");
        vo.setBoardWriter("user00");
        vo.setBoardCategoryName("free");
        boardService.insertBoard(vo);
    }

    @After
    public void AfterTest() throws Exception{
        System.out.println("After Truncate BoardTable");


//        dao.trancate();
    }


    // 게시판에 글을 추가하고 그 글에 다시 받아온다.
    @Test
    public void Test1() throws Exception {

        BoardVO vo = new BoardVO();

        vo.setBoardContent("새로운 본문");
        vo.setBoardTitle("새로운 제목");
        vo.setBoardWriter("user00");
        vo.setBoardCategoryName("free");
        dao.createBoard(vo);

        List<BoardVO> boardList = dao.readAllBoard();

        vo = boardList.get(0);

        System.out.println(vo);
    }

    // 추가되어 있는 글을 가져온다
    @Test
    public void Test2() throws Exception{
        BoardVO vo = dao.readAllBoard().get(0);

        System.out.println(vo);
    }

    // 게시판에 그 글을 업데이트 한다.
    @Test
    public void Test3() throws Exception{
        BoardVO vo;

        List<BoardVO> boardList = dao.readAllBoard();

        System.out.println(Arrays.toString(boardList.toArray()));

        vo = boardList.get(0);

        vo.setBoardContent("업데이트 본문");
        vo.setBoardTitle("업데이트 제목");

        System.out.println(vo);

        dao.updateBoard(vo);

        System.out.println(dao.readAllBoard().get(0).equals(vo));



    }

    // 게시판에 글을 하나 추가하고 그 글을 삭제한다.
    @Test
    public void Test4() throws Exception{
        BoardVO vo = new BoardVO();

        vo.setBoardContent("새로운 본문");
        vo.setBoardTitle("새로운 제목");
        vo.setBoardWriter("user00");
        vo.setBoardCategoryName("free");
        dao.createBoard(vo);
        System.out.println(dao.readAllBoard().size());
        List<BoardVO> boardList = dao.readAllBoard();

        vo = boardList.get(0);

        System.out.println(vo);

        dao.deleteBoard(vo.getBoardId());

        System.out.println(dao.readAllBoard().size());
    }

    //게시판에 모든 글을 가져와서 보여준다.

    @Test
    public void Test5() throws Exception{
        BoardVO vo = new BoardVO();

        vo.setBoardContent("새로운 본문");
        vo.setBoardTitle("새로운 제목");
        vo.setBoardWriter("user00");
        vo.setBoardCategoryName("free");
        dao.createBoard(vo);  dao.createBoard(vo);  dao.createBoard(vo);
        dao.createBoard(vo);  dao.createBoard(vo);  dao.createBoard(vo);

        List<BoardVO> list = dao.readAllBoard();

        System.out.println(Arrays.toString(list.toArray()));
    }

    // 게시판에 free 카테고리를 가져와서 보여준다.

    @Test
    public void Test6() throws Exception{

        BoardVO vo = new BoardVO();

        vo.setBoardContent("새로운 본문");
        vo.setBoardTitle("새로운 제목");
        vo.setBoardWriter("user00");
        vo.setBoardCategoryName("free");

        dao.createBoard(vo);  dao.createBoard(vo);  dao.createBoard(vo);
        dao.createBoard(vo);  dao.createBoard(vo);  dao.createBoard(vo);

        List<BoardVO> list = dao.readCategoryBoard("free");
        List<BoardVO> list2 = dao.readCategoryBoard("QnA");

        System.out.println(Arrays.toString(list.toArray()));
        System.out.println(Arrays.toString(list2.toArray()));
    }


}

```
