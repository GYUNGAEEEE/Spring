#### BoardVO
```java
package com.mycom.board.vo;

public class BoardVO {
	private int no;
	private String writerName;
	private String title;
	private String content;
	
	public BoardVO() {}

	public BoardVO(int no, String writerName, String title, String content) {
		this.no = no;
		this.writerName = writerName;
		this.title = title;
		this.content = content;
	}

	public int getNo() {
		return no;
	}

	public void setNo(int no) {
		this.no = no;
	}

	public String getWriterName() {
		return writerName;
	}

	public void setWriterName(String writerName) {
		this.writerName = writerName;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getContent() {
		return content;
	}

	public void setContent(String content) {
		this.content = content;
	}

	@Override
	public String toString() {
		return "BoardVO [no=" + no + ", writerName=" + writerName + ", title=" + title + ", content=" + content + "]";
	}	
}
```
***
```xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
```
XML 파일의 맨 위에 작성한다.   
MyBatis 매퍼 파일이 특정한 형식을 따라야 한다는 것을 정의하며, MyBatis가 매퍼 파일을 올바르게 해석하고 처리할 수 있도록 도와준다.

```xml
<mapper namespace="com.mycom.board.mapper.BoardMapper">
</mapper>
```
\<mapper> 안에 SQL 매핑 문장들을 작성하면 된다.
- namespace: Mapper 인터페이스

```xml
<mapper namespace="com.mycom.board.mapper.BoardMapper">
	<select id="getList" resultType="com.mycom.board.vo.BoardVO">
		select no, writerName, title, content 
		from board 
		order by no desc
	</select>

	<insert id="writeBoard" parameterType="com.mycom.board.vo.BoardVO">
		insert into board(no, writerName, title, content) 
		values(boardno_seq.nextval, #{writerName}, #{title}, #{content})		
	</insert>

	<update id="updateBoard" parameterType="com.mycom.board.vo.BoardVO">
		update board 
		set title = #{title}, content = #{content} 
		where no = #{no}
	</update>

 	<delete id="deleteBoard" parameterType="int">
 		delete from board 
 		where no = #{no}
 	</delete>
</mapper>
```
SQL 문장에 따라 \<select>, \<insert>, \<update>, \<delete> 를 사용한다.
- id: 메서드 이름
- resultType: SQL 쿼리의 결과를 매핑할 Java 타입을 지정
- parameterType: SQL 쿼리에 전달되는 입력 파라미터의 타입을 지정
- #{}: 파라미터 객체의 getter 메서드를 호출하여 값을 가져온다. 기본 타입일 경우, 해당 파라미터를 직접 사용하여 SQL 쿼리에 값을 바인딩한다.

resultType과 parameterType은 'java.lang.Integer'와 같이 풀네임으로 작성하는 것이 원칙이나 'int'도 가능하다. (String, map 등 alias가 등록된 것들도 있다.)
***
## return 타입이 Map 이라면?
```xml
<select id="getBoardByNo" resultType="java.util.HashMap" parameterType="int">
  select no as bno, writerName, title, content from board
  where no = #{no}
</select>
```
#### Mapper 인터페이스
```java
public HashMap<String, Object> getBoardByNo(int no);
```
#### 테스트
```java
@Test
public void testGetBoardByNo() {
  HashMap<String, Object> map = testMapper.getBoardByNo(2);
  System.out.println(map.keySet());
  System.out.println(map);
  System.out.println(map.get("writerName"));
  System.out.println(map.get("WRITERNAME"));
}
```
```
[BNO, WRITERNAME, TITLE, CONTENT]
{BNO=2, WRITERNAME=작성자2, TITLE=제목2, CONTENT=내용2}
null
작성자2
```
- 컬럼명이 곧 키 값이 된다.
- alias 값도 적용된다.
- 오라클에서 컬럼명은 모두 알파벳 대문자로 저장된다. 따라서, 키 값 역시 대문자로 저장된다. → 대문자로 조회하지 않으면 null 값 반환
## list 안의 map
```xml
<select id="getBoardList" resultType="java.util.HashMap">
  select * from board
  where no between 1 and 5
</select>
```
#### Mapper 인터페이스
```
public ArrayList<HashMap<String, Object>> getBoardList();
```
#### 테스트
```java
@Test
public void testGetBoardList() {
  List<HashMap<String, Object>> boardList = testMapper.getBoardList();
  System.out.println(boardList);
}
```
```
[{NO=1, WRITERNAME=작성자1, TITLE=제목1, CONTENT=내용1}, ..., {NO=5, WRITERNAME=작성자5, TITLE=제목5, CONTENT=내용5}]
```
