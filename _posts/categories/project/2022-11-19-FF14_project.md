---
title:  "FF14_project"
categories:
  - Project
date: 2022-11-19
---

# 🥞11/19 ~ 11/24 백엔드 계획
```
1. Board CRUD 구현
    1-1. Board 글 조회(R)
    1-2. Board 글 쓰기(C)
    1-3. Board 글 수정(U)
    1-4. Board 글 삭제(D)
    1-5. 최종 점검
```

# 📝작업 링크

[FF14_project](https://github.com/goalslee10/FF14_repository_v3.git) -> 메인 \
[jellycodes](https://github.com/jellycodes) -> 내 github

---
수정하게 되면 여기에 계속 업데이트 중...

## 📀 BoardController.java

```java
package dev.info.board.controller;

import java.util.List;

import javax.validation.Valid;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import dev.info.Entity.Board;
import dev.info.board.repository.BoardRepository;
import dev.info.board.service.BoardService;

@RequestMapping("board")
@RestController
@CrossOrigin("*")
public class BoardController {
	@Autowired
	private BoardService boardService;

	@Autowired
	private BoardRepository boardRepository;

	@GetMapping
	 public List<Board.Response> listBoards(){
		
		List<Board> boards = boardService.findallBoards();
		List<Board.Response> response = Board.Response.toResponseList(boards);
		return response;
	}
	
	@PostMapping
	public Board.Response wirteBoard(@RequestBody @Valid Board.Request request) {
		Board board = Board.Request.toEntity(request);
		Board savedBoard = boardService.saveBoard(board);
		return Board.Response.toResponse(savedBoard);
	
	}
	
//	@GetMapping
//	public void findByTitleContaining() {
//		
//		Collection<Board> searchedBoard = boardRepository.findByTitleContaining("제");
//		searchedBoard.forEach(board -> System.out.println(board));
//		
//	}
	
	@GetMapping("/{id}")
	public Board.Response searchBoard(@PathVariable Integer id) {
		Board searchedBoard = boardService.findBoardById(id);
		return Board.Response.toResponse(searchedBoard);
	}
	
	@PutMapping
	public List<Board.Response> modifyBoard(@RequestBody Board.Request request) {
		List<Board> boards = boardService.modifyBoard(request);
		return Board.Response.toResponseList(boards);
	}
	
	@DeleteMapping
	public List<Board.Response> deleteBoard(@RequestParam("id") Integer id) {
		List<Board> boards = boardService.deleteBoard(id);
		return Board.Response.toResponseList(boards);
	}

}
```


## 📀 BoardService.java

```java
package dev.info.board.service;

import java.util.List;

import org.springframework.stereotype.Service;

import dev.info.Entity.Board;

public interface BoardService {

	List<Board> findallBoards(); // 게시판 글 보기
	
	Board findBoardById(Integer id);
	
	Board saveBoard(Board newBoard); // 게시판 글 쓰기
	
	List<Board> modifyBoard(Board.Request request);
	
	List<Board> deleteBoard(Integer id); // 게시판 글 지우기
}
```

## 📀 BoardServiceImpl.java

```java
package dev.info.board.service;

import java.util.List;
import java.util.Optional;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import dev.info.Entity.Board;
import dev.info.Entity.Board.Request;
import dev.info.board.repository.BoardRepository;

@Service
public class BoardServiceImpl implements BoardService {

	@Autowired
	private BoardRepository boardRepository;

	//findAllId
	//findBoardById
	//findBoardByTitle
	
	@Override
	public List<Board> findallBoards() {
		return boardRepository.findAll();
	}

	@Override
	public Board findBoardById(Integer id) {
		Board searchedBoard = boardRepository.findById(id)
				.orElseThrow(() -> new RuntimeException(String.format("%s에 해당하는 Id가 존재하지 않습니다.", id)));
		return searchedBoard;
	}

	@Override
	public Board saveBoard(Board newBoard) {
		return boardRepository.save(newBoard);
	}

	@Override
	public List<Board> modifyBoard(Request request) {
		final Optional<Board> board = boardRepository.findById(request.getId());

		if (board.isPresent()) {
			final Board searchedBoard = board.get();

			searchedBoard.setTitle(request.getTitle());
			searchedBoard.setContent(request.getContent());

			boardRepository.save(searchedBoard);
		}

		List<Board> boards = boardRepository.findAll();
		return boards;
	}

	@Override
	public List<Board> deleteBoard(Integer id) {
		boardRepository.deleteById(id);

		List<Board> boards =  boardRepository.findAll();
		return boards;
	}

}
```