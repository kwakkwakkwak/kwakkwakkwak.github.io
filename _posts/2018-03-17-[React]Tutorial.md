---
title: "[React]Tutorial"
description: "React 공식 Tutorial"
categories: "React Tutorial"
tags: React Tutorial
date: 2018-03-17
comments : false
---

실제 구현 코드는 [여기](https://github.com/kwakkwakkwak/reactTutorial)에서 확인

# React Tutorial(tic-tac-toe GAME)

## Import 

* 처음엔 react 에 react-dom 이 같이 있었지만 따로 쓸 경우가 생겨서 분리 해두었다 합니다.

```JSX

import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';

```

## Square

* tic-tac-toe 의 가장 기본이되는 상자 한개를 만들 함수다.

* button 의 onClick 메소드와 값을 모두 상위 컴포넌트에서 받아온다.


```JSX


function Square(props) {
    return (
        <button className="square" onClick={props.onClick}>
            {props.value}
        </button>
    );
}

```

## Board

* `Square`의 상위컴포넌트 상자 9개를 배열로 만들어서 랜더링한다.

* 그리고 위에서 만든 `Square`를 랜더링 하는데 여기도 역시 실제로 모든 코드가 동작되게 만들지 않고 override 되서 만들게 되어있다.

```JSX
class Board extends React.Component {
    renderSquare(i) {
        return (
            <Square
                value={this.props.squares[i]}
                onClick={() => this.props.onClick(i)}
            />
        );
    }

    render() {
        return (
            <div>
                <div className="board-row">
                    {this.renderSquare(0)}
                    {this.renderSquare(1)}
                    {this.renderSquare(2)}
                </div>
                <div className="board-row">
                    {this.renderSquare(3)}
                    {this.renderSquare(4)}
                    {this.renderSquare(5)}
                </div>
                <div className="board-row">
                    {this.renderSquare(6)}
                    {this.renderSquare(7)}
                    {this.renderSquare(8)}
                </div>
            </div>
        );
    }
}
```

## Game

* 이 프로젝트에서 최상위 컴포넌트이며 `Square` 를 실제 메소드가 실행되고, 값을 추가한다.

* `handleClick` 메소드가 실질적으로 `Square` 의 onClick 이다.

* 게임의 모든 변수는 `Game`에 저장되어있고, 하위컴포넌트로 그상태를 전달한다.  

```JSX
class Game extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            history: [{
                squares: Array(9).fill(null)
            }],
            stepNumber: 0,
            xIsNext: true
        };
    }
    jumpTo(step) {
        this.setState({
            stepNumber: step,
            xIsNext: (step % 2) === 0,
        });
    }

    handleClick(i) {
        const history = this.state.history.slice(0, this.state.stepNumber + 1);
        const current = history[history.length - 1];
        const squares = current.squares.slice();
        if (calculateWinner(squares) || squares[i]) {
            return;
        }
        squares[i] = this.state.xIsNext ? 'X' : 'O';
        this.setState({
            history: history.concat([{
                squares: squares
            }]),
            stepNumber: history.length,
            xIsNext: !this.state.xIsNext,
        });
    }

    render() {
        const history = this.state.history;
        const current = history[this.state.stepNumber];
        const winner = calculateWinner(current.squares);

        const moves = history.map((step, move) => {
            const desc = move ?
                'Go to move #' + move :
                'Go to game start';
            return (
                <li key ={move}>
                    <button onClick={() => this.jumpTo(move)}>{desc}</button>
                </li>
            );
        });

        let status;
        if (winner) {
            status = 'Winner: ' + winner;
        } else {
            status = 'Next player: ' + (this.state.xIsNext ? 'X' : 'O');
        }

        return (
            <div className="game">
                <div className="game-board">
                    <Board
                        squares={current.squares}
                        onClick={(i) => this.handleClick(i)}
                    />
                </div>
                <div className="game-info">
                    <div>{status}</div>
                    <ol>{moves}</ol>
                </div>
            </div>
        );
    }
}


function calculateWinner(squares) {
    const lines = [
        [0, 1, 2],
        [3, 4, 5],
        [6, 7, 8],
        [0, 3, 6],
        [1, 4, 7],
        [2, 5, 8],
        [0, 4, 8],
        [2, 4, 6],
    ];
    for (let i = 0; i < lines.length; i++) {
        const [a, b, c] = lines[i];
        if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
            return squares[a];
        }
    }
    return null;
}

```

## ReactDom

* 마지막으로 `ReactDom` 으로 최상위 컴포넌트를 실제 태그안에 랜더링 한다.

* 

```JSX

ReactDOM.render(
    <Game />,
    document.getElementById('root')
);

```