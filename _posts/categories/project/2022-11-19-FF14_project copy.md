---
title:  "FF14_project"
categories:
  - Project
date: 2022-11-19
---

# π₯11/19 ~ 11/24 λ°±μλ κ³ν
```
1. Board CRUD κ΅¬ν
    1-1. Board κΈ μ‘°ν(R)
    1-2. Board κΈ μ°κΈ°(C)
    1-3. Board κΈ μμ (U)
    1-4. Board κΈ μ­μ (D)
    1-5. μ΅μ’ μ κ²
```

# πμμ λ§ν¬

[FF14_project](https://github.com/goalslee10/FF14_repository_v3.git) -> λ©μΈ \
[jellycodes](https://github.com/jellycodes) -> λ΄ github

---
μμ νκ² λλ©΄ μ¬κΈ°μ κ³μ μλ°μ΄νΈ μ€...

## π BoardController.java

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
//		Collection<Board> searchedBoard = boardRepository.findByTitleContaining("μ ");
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


## π BoardService.java

```java
package dev.info.board.service;

import java.util.List;

import org.springframework.stereotype.Service;

import dev.info.Entity.Board;

public interface BoardService {

	List<Board> findallBoards(); // κ²μν κΈ λ³΄κΈ°
	
	Board findBoardById(Integer id);
	
	Board saveBoard(Board newBoard); // κ²μν κΈ μ°κΈ°
	
	List<Board> modifyBoard(Board.Request request);
	
	List<Board> deleteBoard(Integer id); // κ²μν κΈ μ§μ°κΈ°
}
```

## π BoardServiceImpl.java

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
				.orElseThrow(() -> new RuntimeException(String.format("%sμ ν΄λΉνλ Idκ° μ‘΄μ¬νμ§ μμ΅λλ€.", id)));
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