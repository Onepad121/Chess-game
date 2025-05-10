<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Islamic Chess</title>
    <style>
        :root {
            --light-square: #f0d9b5;
            --dark-square: #b58863;
            --highlight: rgba(0, 255, 0, 0.4);
            --move-hint: rgba(0, 0, 255, 0.3);
            --check: rgba(255, 0, 0, 0.5);
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #1e3c72, #2a5298);
            margin: 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            color: white;
            min-height: 100vh;
        }

        .game-container {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 30px;
            max-width: 1200px;
            width: 100%;
        }

        .board-container {
            position: relative;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
            border-radius: 8px;
            overflow: hidden;
        }

        #chessboard {
            display: grid;
            grid-template-columns: repeat(8, 1fr);
            grid-template-rows: repeat(8, 1fr);
            width: 600px;
            height: 600px;
            background-color: #fff;
        }

        .square {
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 48px;
            font-weight: bold;
            cursor: pointer;
            user-select: none;
            position: relative;
        }

        .light {
            background-color: var(--light-square);
            color: #333;
        }

        .dark {
            background-color: var(--dark-square);
            color: #fff;
        }

        .highlight {
            background-color: var(--highlight) !important;
        }

        .move-hint {
            position: absolute;
            width: 30px;
            height: 30px;
            border-radius: 50%;
            background-color: var(--move-hint);
            opacity: 0.7;
        }

        .check {
            background-color: var(--check) !important;
        }

        .coordinates {
            position: absolute;
            font-size: 12px;
            font-weight: bold;
            color: rgba(255, 255, 255, 0.7);
        }

        .file-coordinate {
            bottom: 2px;
            right: 5px;
        }

        .rank-coordinate {
            top: 2px;
            left: 5px;
        }

        .panel {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 10px;
            padding: 20px;
            width: 300px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
        }

        h1 {
            text-align: center;
            margin-bottom: 30px;
            font-weight: 300;
            text-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
        }

        .btn {
            background: linear-gradient(135deg, #4a6bff, #6a11cb);
            color: white;
            border: none;
            padding: 12px 20px;
            margin: 5px 0;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
            width: 100%;
            transition: all 0.3s;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }

        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 8px rgba(0, 0, 0, 0.15);
        }

        .btn:active {
            transform: translateY(0);
        }

        .btn-secondary {
            background: rgba(255, 255, 255, 0.1);
        }

        .btn-group {
            display: flex;
            gap: 10px;
        }

        .btn-group .btn {
            flex: 1;
        }

        .game-info {
            margin-top: 20px;
            padding: 15px;
            background: rgba(0, 0, 0, 0.2);
            border-radius: 5px;
        }

        .game-info p {
            margin: 5px 0;
        }

        select {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border-radius: 5px;
            background: rgba(255, 255, 255, 0.9);
            border: none;
        }

        .move-history {
            height: 200px;
            overflow-y: auto;
            background: rgba(0, 0, 0, 0.2);
            padding: 10px;
            border-radius: 5px;
            margin-top: 10px;
        }

        .move-entry {
            display: flex;
            justify-content: space-between;
            padding: 5px 0;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }

        .promotion-modal {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.7);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 100;
            display: none;
        }

        .promotion-options {
            background: rgba(255, 255, 255, 0.9);
            padding: 20px;
            border-radius: 10px;
            display: flex;
            gap: 15px;
        }

        .promotion-option {
            font-size: 40px;
            padding: 10px 20px;
            cursor: pointer;
            border-radius: 5px;
            background: rgba(0, 0, 0, 0.1);
        }

        .promotion-option:hover {
            background: rgba(0, 0, 0, 0.2);
        }

        @media (max-width: 1000px) {
            .game-container {
                flex-direction: column;
                align-items: center;
            }

            #chessboard {
                width: 90vw;
                height: 90vw;
                max-width: 500px;
                max-height: 500px;
            }

            .panel {
                width: 90vw;
                max-width: 500px;
            }

            .square {
                font-size: 6vw;
            }
        }
    </style>
</head>
<body>
    <h1>Islamic Chess</h1>
    
    <div class="game-container">
        <div class="board-container">
            <div id="chessboard"></div>
            <div class="promotion-modal" id="promotionModal">
                <div class="promotion-options">
                    <div class="promotion-option" data-piece="q">q</div>
                    <div class="promotion-option" data-piece="r">r</div>
                    <div class="promotion-option" data-piece="b">b</div>
                    <div class="promotion-option" data-piece="n">n</div>
                </div>
            </div>
        </div>
        
        <div class="panel">
            <div class="btn-group">
                <button id="newGameBtn" class="btn">New Game</button>
                <button id="undoBtn" class="btn btn-secondary">Undo</button>
            </div>
            
            <select id="gameMode">
                <option value="friend">Play vs Friend</option>
                <option value="computer">Play vs Computer</option>
            </select>
            
            <select id="difficulty" style="display: none;">
                <option value="easy">Easy</option>
                <option value="medium" selected>Medium</option>
                <option value="hard">Hard</option>
            </select>
            
            <button id="hintBtn" class="btn btn-secondary">Show Hint</button>
            
            <div class="game-info">
                <p>Turn: <span id="turnIndicator">White</span></p>
                <p>Status: <span id="gameStatus">Playing</span></p>
                <p>Last Move: <span id="lastMove">-</span></p>
            </div>
            
            <div class="move-history" id="moveHistory"></div>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            // Game state
            const gameState = {
                board: Array(8).fill().map(() => Array(8).fill(null)),
                turn: 'white',
                selectedSquare: null,
                validMoves: [],
                moveHistory: [],
                gameOver: false,
                check: false,
                whiteKingPos: { row: 7, col: 4 },
                blackKingPos: { row: 0, col: 4 },
                whiteCanCastle: { kingside: true, queenside: true },
                blackCanCastle: { kingside: true, queenside: true },
                enPassantTarget: null,
                halfmoveClock: 0,
                fullmoveNumber: 1,
                vsComputer: false,
                computerDifficulty: 'medium',
                promotionPending: null
            };

            // DOM elements
            const chessboard = document.getElementById('chessboard');
            const turnIndicator = document.getElementById('turnIndicator');
            const gameStatus = document.getElementById('gameStatus');
            const lastMove = document.getElementById('lastMove');
            const moveHistory = document.getElementById('moveHistory');
            const newGameBtn = document.getElementById('newGameBtn');
            const undoBtn = document.getElementById('undoBtn');
            const gameMode = document.getElementById('gameMode');
            const difficulty = document.getElementById('difficulty');
            const hintBtn = document.getElementById('hintBtn');
            const promotionModal = document.getElementById('promotionModal');
            const promotionOptions = document.querySelectorAll('.promotion-option');

            // Initialize the board
            function initializeBoard() {
                chessboard.innerHTML = '';
                
                // Create squares
                for (let row = 0; row < 8; row++) {
                    for (let col = 0; col < 8; col++) {
                        const square = document.createElement('div');
                        square.className = `square ${(row + col) % 2 === 0 ? 'light' : 'dark'}`;
                        square.dataset.row = row;
                        square.dataset.col = col;
                        
                        // Add coordinates
                        if (col === 7) {
                            const rankCoord = document.createElement('span');
                            rankCoord.className = 'coordinates rank-coordinate';
                            rankCoord.textContent = 8 - row;
                            square.appendChild(rankCoord);
                        }
                        
                        if (row === 7) {
                            const fileCoord = document.createElement('span');
                            fileCoord.className = 'coordinates file-coordinate';
                            fileCoord.textContent = String.fromCharCode(97 + col);
                            square.appendChild(fileCoord);
                        }
                        
                        square.addEventListener('click', () => handleSquareClick(row, col));
                        chessboard.appendChild(square);
                    }
                }
                
                // Set up initial piece positions
                resetGame();
            }

            // Reset the game
            function resetGame() {
                // Clear the board
                for (let row = 0; row < 8; row++) {
                    for (let col = 0; col < 8; col++) {
                        gameState.board[row][col] = null;
                        updateSquareVisual(row, col);
                    }
                }
                
                // Set up pawns
                for (let col = 0; col < 8; col++) {
                    gameState.board[1][col] = { type: 'p', color: 'black' };
                    gameState.board[6][col] = { type: 'p', color: 'white' };
                }
                
                // Set up other pieces
                const pieceOrder = ['r', 'n', 'b', 'q', 'k', 'b', 'n', 'r'];
                for (let col = 0; col < 8; col++) {
                    gameState.board[0][col] = { type: pieceOrder[col], color: 'black' };
                    gameState.board[7][col] = { type: pieceOrder[col], color: 'white' };
                }
                
                // Reset game state
                gameState.turn = 'white';
                gameState.selectedSquare = null;
                gameState.validMoves = [];
                gameState.moveHistory = [];
                gameState.gameOver = false;
                gameState.check = false;
                gameState.whiteKingPos = { row: 7, col: 4 };
                gameState.blackKingPos = { row: 0, col: 4 };
                gameState.whiteCanCastle = { kingside: true, queenside: true };
                gameState.blackCanCastle = { kingside: true, queenside: true };
                gameState.enPassantTarget = null;
                gameState.halfmoveClock = 0;
                gameState.fullmoveNumber = 1;
                gameState.promotionPending = null;
                
                // Update UI
                updateUI();
                updateBoardVisual();
                
                // If playing against computer and it's their turn, make a move
                if (gameState.vsComputer && gameState.turn === 'black') {
                    setTimeout(makeComputerMove, 500);
                }
            }

            // Update square visual
            function updateSquareVisual(row, col) {
                const square = document.querySelector(`.square[data-row="${row}"][data-col="${col}"]`);
                square.innerHTML = '';
                
                // Add coordinates back
                if (col === 7) {
                    const rankCoord = document.createElement('span');
                    rankCoord.className = 'coordinates rank-coordinate';
                    rankCoord.textContent = 8 - row;
                    square.appendChild(rankCoord);
                }
                
                if (row === 7) {
                    const fileCoord = document.createElement('span');
                    fileCoord.className = 'coordinates file-coordinate';
                    fileCoord.textContent = String.fromCharCode(97 + col);
                    square.appendChild(fileCoord);
                }
                
                // Add piece if exists
                const piece = gameState.board[row][col];
                if (piece) {
                    const pieceElement = document.createElement('div');
                    pieceElement.textContent = piece.type;
                    pieceElement.style.color = piece.color === 'white' ? '#fff' : '#000';
                    square.appendChild(pieceElement);
                }
                
                // Clear highlights
                square.classList.remove('highlight', 'check');
                
                // Clear move hints
                const existingHints = square.querySelectorAll('.move-hint');
                existingHints.forEach(hint => hint.remove());
            }

            // Update the entire board visual
            function updateBoardVisual() {
                for (let row = 0; row < 8; row++) {
                    for (let col = 0; col < 8; col++) {
                        updateSquareVisual(row, col);
                    }
                }
                
                // Highlight selected square
                if (gameState.selectedSquare) {
                    const { row, col } = gameState.selectedSquare;
                    const square = document.querySelector(`.square[data-row="${row}"][data-col="${col}"]`);
                    square.classList.add('highlight');
                }
                
                // Highlight valid moves
                gameState.validMoves.forEach(move => {
                    const square = document.querySelector(`.square[data-row="${move.row}"][data-col="${move.col}"]`);
                    const hint = document.createElement('div');
                    hint.className = 'move-hint';
                    square.appendChild(hint);
                });
                
                // Highlight king in check
                const kingPos = gameState.turn === 'white' ? gameState.whiteKingPos : gameState.blackKingPos;
                if (gameState.check && isSquareAttacked(kingPos.row, kingPos.col, gameState.turn)) {
                    const square = document.querySelector(`.square[data-row="${kingPos.row}"][data-col="${kingPos.col}"]`);
                    square.classList.add('check');
                }
            }

            // Update UI elements
            function updateUI() {
                turnIndicator.textContent = gameState.turn === 'white' ? 'White' : 'Black';
                turnIndicator.style.color = gameState.turn === 'white' ? '#fff' : '#000';
                
                if (gameState.gameOver) {
                    gameStatus.textContent = 'Game Over';
                } else if (gameState.check) {
                    gameStatus.textContent = 'Check!';
                } else {
                    gameStatus.textContent = 'Playing';
                }
                
                // Update move history
                moveHistory.innerHTML = '';
                for (let i = 0; i < gameState.moveHistory.length; i += 2) {
                    const moveEntry = document.createElement('div');
                    moveEntry.className = 'move-entry';
                    
                    const whiteMove = document.createElement('span');
                    whiteMove.textContent = `${i/2 + 1}. ${gameState.moveHistory[i]}`;
                    moveEntry.appendChild(whiteMove);
                    
                    if (i + 1 < gameState.moveHistory.length) {
                        const blackMove = document.createElement('span');
                        blackMove.textContent = gameState.moveHistory[i + 1];
                        moveEntry.appendChild(blackMove);
                    }
                    
                    moveHistory.appendChild(moveEntry);
                }
                moveHistory.scrollTop = moveHistory.scrollHeight;
                
                // Update last move
                if (gameState.moveHistory.length > 0) {
                    lastMove.textContent = gameState.moveHistory[gameState.moveHistory.length - 1];
                } else {
                    lastMove.textContent = '-';
                }
            }

            // Handle square click
            function handleSquareClick(row, col) {
                if (gameState.gameOver || gameState.promotionPending) return;
                
                const piece = gameState.board[row][col];
                
                // If a piece of the current turn's color is clicked, select it
                if (piece && piece.color === gameState.turn) {
                    gameState.selectedSquare = { row, col };
                    gameState.validMoves = getValidMoves(row, col);
                    updateBoardVisual();
                    return;
                }
                
                // If a square is already selected and the clicked square is a valid move, make the move
                if (gameState.selectedSquare && gameState.validMoves.some(move => move.row === row && move.col === col)) {
                    makeMove(gameState.selectedSquare.row, gameState.selectedSquare.col, row, col);
                    return;
                }
                
                // Otherwise, deselect
                gameState.selectedSquare = null;
                gameState.validMoves = [];
                updateBoardVisual();
            }

            // Get valid moves for a piece
            function getValidMoves(row, col) {
                const piece = gameState.board[row][col];
                if (!piece) return [];
                
                const moves = [];
                
                switch (piece.type) {
                    case 'p': // Pawn
                        const direction = piece.color === 'white' ? -1 : 1;
                        const startRow = piece.color === 'white' ? 6 : 1;
                        
                        // Forward move
                        if (isInBounds(row + direction, col) && !gameState.board[row + direction][col]) {
                            moves.push({ row: row + direction, col });
                            
                            // Double move from starting position
                            if (row === startRow && !gameState.board[row + 2 * direction][col]) {
                                moves.push({ row: row + 2 * direction, col });
                            }
                        }
                        
                        // Captures
                        for (const dc of [-1, 1]) {
                            const newCol = col + dc;
                            if (isInBounds(row + direction, newCol)) {
                                // Normal capture
                                if (gameState.board[row + direction][newCol] && gameState.board[row + direction][newCol].color !== piece.color) {
                                    moves.push({ row: row + direction, col: newCol });
                                }
                                
                                // En passant
                                if (gameState.enPassantTarget && gameState.enPassantTarget.row === row + direction && gameState.enPassantTarget.col === newCol) {
                                    moves.push({ row: row + direction, col: newCol, isEnPassant: true });
                                }
                            }
                        }
                        break;
                        
                    case 'r': // Rook
                        addStraightMoves(row, col, piece.color, moves);
                        break;
                        
                    case 'n': // Knight
                        const knightMoves = [
                            { dr: 2, dc: 1 }, { dr: 1, dc: 2 },
                            { dr: -1, dc: 2 }, { dr: -2, dc: 1 },
                            { dr: -2, dc: -1 }, { dr: -1, dc: -2 },
                            { dr: 1, dc: -2 }, { dr: 2, dc: -1 }
                        ];
                        
                        for (const move of knightMoves) {
                            const newRow = row + move.dr;
                            const newCol = col + move.dc;
                            
                            if (isInBounds(newRow, newCol) {
                                const target = gameState.board[newRow][newCol];
                                if (!target || target.color !== piece.color) {
                                    moves.push({ row: newRow, col: newCol });
                                }
                            }
                        }
                        break;
                        
                    case 'b': // Bishop
                        addDiagonalMoves(row, col, piece.color, moves);
                        break;
                        
                    case 'q': // Queen
                        addStraightMoves(row, col, piece.color, moves);
                        addDiagonalMoves(row, col, piece.color, moves);
                        break;
                        
                    case 'k': // King
                        for (let dr = -1; dr <= 1; dr++) {
                            for (let dc = -1; dc <= 1; dc++) {
                                if (dr === 0 && dc === 0) continue;
                                
                                const newRow = row + dr;
                                const newCol = col + dc;
                                
                                if (isInBounds(newRow, newCol)) {
                                    const target = gameState.board[newRow][newCol];
                                    if (!target || target.color !== piece.color) {
                                        // Temporarily make the move to check if it puts king in check
                                        const original = target;
                                        gameState.board[newRow][newCol] = { type: 'k', color: piece.color };
                                        gameState.board[row][col] = null;
                                        
                                        const kingPos = piece.color === 'white' ? 
                                            { row: newRow, col: newCol } : 
                                            { row: newRow, col: newCol };
                                        
                                        if (!isSquareAttacked(kingPos.row, kingPos.col, piece.color)) {
                                            moves.push({ row: newRow, col: newCol });
                                        }
                                        
                                        // Undo temporary move
                                        gameState.board[newRow][newCol] = original;
                                        gameState.board[row][col] = { type: 'k', color: piece.color };
                                    }
                                }
                            }
                        }
                        
                        // Castling
                        if (piece.color === 'white' && row === 7 && col === 4) {
                            // Kingside
                            if (gameState.whiteCanCastle.kingside && 
                                !gameState.board[7][5] && 
                                !gameState.board[7][6] && 
                                !isSquareAttacked(7, 4, 'white') && 
                                !isSquareAttacked(7, 5, 'white') && 
                                !isSquareAttacked(7, 6, 'white')) {
                                moves.push({ row: 7, col: 6, isCastle: true });
                            }
                            
                            // Queenside
                            if (gameState.whiteCanCastle.queenside && 
                                !gameState.board[7][3] && 
                                !gameState.board[7][2] && 
                                !gameState.board[7][1] && 
                                !isSquareAttacked(7, 4, 'white') && 
                                !isSquareAttacked(7, 3, 'white') && 
                                !isSquareAttacked(7, 2, 'white')) {
                                moves.push({ row: 7, col: 2, isCastle: true });
                            }
                        } else if (piece.color === 'black' && row === 0 && col === 4) {
                            // Kingside
                            if (gameState.blackCanCastle.kingside && 
                                !gameState.board[0][5] && 
                                !gameState.board[0][6] && 
                                !isSquareAttacked(0, 4, 'black') && 
                                !isSquareAttacked(0, 5, 'black') && 
                                !isSquareAttacked(0, 6, 'black')) {
                                moves.push({ row: 0, col: 6, isCastle: true });
                            }
                            
                            // Queenside
                            if (gameState.blackCanCastle.queenside && 
                                !gameState.board[0][3] && 
                                !gameState.board[0][2] && 
                                !gameState.board[0][1] && 
                                !isSquareAttacked(0, 4, 'black') && 
                                !isSquareAttacked(0, 3, 'black') && 
                                !isSquareAttacked(0, 2, 'black')) {
                                moves.push({ row: 0, col: 2, isCastle: true });
                            }
                        }
                        break;
                }
                
                // Filter out moves that would leave king in check
                return moves.filter(move => {
                    // Temporarily make the move
                    const originalPiece = gameState.board[move.row][move.col];
                    const movingPiece = gameState.board[row][col];
                    
                    gameState.board[move.row][move.col] = movingPiece;
                    gameState.board[row][col] = null;
                    
                    // Special handling for castling
                    let rookMove = null;
                    if (move.isCastle) {
                        if (move.col === 6) { // Kingside
                            rookMove = { fromRow: move.row, fromCol: 7, toRow: move.row, toCol: 5 };
                            gameState.board[move.row][5] = gameState.board[move.row][7];
                            gameState.board[move.row][7] = null;
                        } else if (move.col === 2) { // Queenside
                            rookMove = { fromRow: move.row, fromCol: 0, toRow: move.row, toCol: 3 };
                            gameState.board[move.row][3] = gameState.board[move.row][0];
                            gameState.board[move.row][0] = null;
                        }
                    }
                    
                    // Check if king is in check after move
                    const kingPos = movingPiece.color === 'white' ? 
                        (movingPiece.type === 'k' ? { row: move.row, col: move.col } : gameState.whiteKingPos) :
                        (movingPiece.type === 'k' ? { row: move.row, col: move.col } : gameState.blackKingPos);
                    
                    const inCheck = isSquareAttacked(kingPos.row, kingPos.col, movingPiece.color);
                    
                    // Undo the move
                    gameState.board[row][col] = movingPiece;
                    gameState.board[move.row][move.col] = originalPiece;
                    
                    // Undo rook move if castling
                    if (rookMove) {
                        gameState.board[rookMove.fromRow][rookMove.fromCol] = gameState.board[rookMove.toRow][rookMove.toCol];
                        gameState.board[rookMove.toRow][rookMove.toCol] = null;
                    }
                    
                    return !inCheck;
                });
            }

            // Add straight moves (for rook and queen)
            function addStraightMoves(row, col, color, moves) {
                const directions = [
                    { dr: 1, dc: 0 },  // down
                    { dr: -1, dc: 0 }, // up
                    { dr: 0, dc: 1 },  // right
                    { dr: 0, dc: -1 }  // left
                ];
                
                for (const dir of directions) {
                    for (let i = 1; i < 8; i++) {
                        const newRow = row + dir.dr * i;
                        const newCol = col + dir.dc * i;
                        
                        if (!isInBounds(newRow, newCol)) break;
                        
                        const target = gameState.board[newRow][newCol];
                        if (!target) {
                            moves.push({ row: newRow, col: newCol });
                        } else {
                            if (target.color !== color) {
                                moves.push({ row: newRow, col: newCol });
                            }
                            break;
                        }
                    }
                }
            }

            // Add diagonal moves (for bishop and queen)
            function addDiagonalMoves(row, col, color, moves) {
                const directions = [
                    { dr: 1, dc: 1 },   // down-right
                    { dr: 1, dc: -1 },   // down-left
                    { dr: -1, dc: 1 },   // up-right
                    { dr: -1, dc: -1 }   // up-left
                ];
                
                for (const dir of directions) {
                    for (let i = 1; i < 8; i++) {
                        const newRow = row + dir.dr * i;
                        const newCol = col + dir.dc * i;
                        
                        if (!isInBounds(newRow, newCol)) break;
                        
                        const target = gameState.board[newRow][newCol];
                        if (!target) {
                            moves.push({ row: newRow, col: newCol });
                        } else {
                            if (target.color !== color) {
                                moves.push({ row: newRow, col: newCol });
                            }
                            break;
                        }
                    }
                }
            }

            // Check if coordinates are within bounds
            function isInBounds(row, col) {
                return row >= 0 && row < 8 && col >= 0 && col < 8;
            }

            // Make a move
            function makeMove(fromRow, fromCol, toRow, toCol, promotionChoice = null) {
                const movingPiece = gameState.board[fromRow][fromCol];
                const targetPiece = gameState.board[toRow][toCol];
                
                // Check if it's the correct turn
                if (movingPiece.color !== gameState.turn) return false;
                
                // Check if the move is valid
                const validMoves = getValidMoves(fromRow, fromCol);
                const move = validMoves.find(m => m.row === toRow && m.col === toCol);
                if (!move) return false;
                
                // Create move notation
                let notation = '';
                const pieceType = movingPiece.type === 'p' ? '' : movingPiece.type.toUpperCase();
                
                // For captures, add x and the file/rank if needed for disambiguation
                if (targetPiece || move.isEnPassant) {
                    if (pieceType === '') {
                        notation += String.fromCharCode(97 + fromCol);
                    }
                    notation += 'x';
                }
                
                notation += String.fromCharCode(97 + toCol) + (8 - toRow);
                
                // Handle special moves
                if (move.isCastle) {
                    notation = toCol === 6 ? 'O-O' : 'O-O-O';
                }
                
                // Handle pawn promotion
                let promotedPiece = null;
                if (movingPiece.type === 'p' && (toRow === 0 || toRow === 7)) {
                    if (!promotionChoice) {
                        // Show promotion modal
                        gameState.promotionPending = { fromRow, fromCol, toRow, toCol };
                        promotionModal.style.display = 'flex';
                        return false;
                    }
                    
                    promotedPiece = { type: promotionChoice, color: movingPiece.color };
                    notation += '=' + promotionChoice.toUpperCase();
                }
                
                // Record the move
                gameState.moveHistory.push(notation);
                
                // Update castling rights
                if (movingPiece.type === 'k') {
                    if (movingPiece.color === 'white') {
                        gameState.whiteCanCastle = { kingside: false, queenside: false };
                    } else {
                        gameState.blackCanCastle = { kingside: false, queenside: false };
                    }
                    
                    // Handle castling
                    if (move.isCastle) {
                        if (toCol === 6) { // Kingside
                            gameState.board[toRow][5] = gameState.board[toRow][7];
                            gameState.board[toRow][7] = null;
                        } else if (toCol === 2) { // Queenside
                            gameState.board[toRow][3] = gameState.board[toRow][0];
                            gameState.board[toRow][0] = null;
                        }
                    }
                }
                
                if (movingPiece.type === 'r') {
                    if (movingPiece.color === 'white') {
                        if (fromCol === 7) gameState.whiteCanCastle.kingside = false;
                        if (fromCol === 0) gameState.whiteCanCastle.queenside = false;
                    } else {
                        if (fromCol === 7) gameState.blackCanCastle.kingside = false;
                        if (fromCol === 0) gameState.blackCanCastle.queenside = false;
                    }
                }
                
                // Handle en passant
                if (move.isEnPassant) {
                    gameState.board[fromRow][toCol] = null;
                }
                
                // Update en passant target
                gameState.enPassantTarget = null;
                if (movingPiece.type === 'p' && Math.abs(fromRow - toRow) === 2) {
                    gameState.enPassantTarget = { row: fromRow + (toRow - fromRow) / 2, col: fromCol };
                }
                
                // Update halfmove clock (for 50-move rule)
                if (movingPiece.type === 'p' || targetPiece) {
                    gameState.halfmoveClock = 0;
                } else {
                    gameState.halfmoveClock++;
                }
                
                // Update fullmove number after black's move
                if (gameState.turn === 'black') {
                    gameState.fullmoveNumber++;
                }
                
                // Move the piece
                gameState.board[toRow][toCol] = promotedPiece || movingPiece;
                gameState.board[fromRow][fromCol] = null;
                
                // Update king position
                if (movingPiece.type === 'k') {
                    if (movingPiece.color === 'white') {
                        gameState.whiteKingPos = { row: toRow, col: toCol };
                    } else {
                        gameState.blackKingPos = { row: toRow, col: toCol };
                    }
                }
                
                // Switch turns
                gameState.turn = gameState.turn === 'white' ? 'black' : 'white';
                
                // Check for check/checkmate/stalemate
                const opponentColor = gameState.turn;
                const opponentKingPos = opponentColor === 'white' ? gameState.whiteKingPos : gameState.blackKingPos;
                gameState.check = isSquareAttacked(opponentKingPos.row, opponentKingPos.col, opponentColor);
                
                // Check for checkmate or stalemate
                const hasLegalMoves = hasAnyLegalMoves(opponentColor);
                if (!hasLegalMoves) {
                    gameState.gameOver = true;
                    if (gameState.check) {
                        gameStatus.textContent = opponentColor === 'white' ? 'Black wins by checkmate!' : 'White wins by checkmate!';
                    } else {
                        gameStatus.textContent = 'Stalemate!';
                    }
                }
                
                // Update UI
                gameState.selectedSquare = null;
                gameState.validMoves = [];
                updateBoardVisual();
                updateUI();
                
                // If playing against computer and it's their turn, make a move
                if (gameState.vsComputer && gameState.turn === 'black' && !gameState.gameOver) {
                    setTimeout(makeComputerMove, 500);
                }
                
                return true;
            }

            // Check if a square is attacked by the opponent
            function isSquareAttacked(row, col, color) {
                const opponentColor = color === 'white' ? 'black' : 'white';
                
                // Check for pawn attacks
                const pawnDirection = color === 'white' ? 1 : -1;
                for (const dc of [-1, 1]) {
                    const newRow = row + pawnDirection;
                    const newCol = col + dc;
                    
                    if (isInBounds(newRow, newCol)) {
                        const piece = gameState.board[newRow][newCol];
                        if (piece && piece.color === opponentColor && piece.type === 'p') {
                            return true;
                        }
                    }
                }
                
                // Check for knight attacks
                const knightMoves = [
                    { dr: 2, dc: 1 }, { dr: 1, dc: 2 },
                    { dr: -1, dc: 2 }, { dr: -2, dc: 1 },
                    { dr: -2, dc: -1 }, { dr: -1, dc: -2 },
                    { dr: 1, dc: -2 }, { dr: 2, dc: -1 }
                ];
                
                for (const move of knightMoves) {
                    const newRow = row + move.dr;
                    const newCol = col + move.dc;
                    
                    if (isInBounds(newRow, newCol)) {
                        const piece = gameState.board[newRow][newCol];
                        if (piece && piece.color === opponentColor && piece.type === 'n') {
                            return true;
                        }
                    }
                }
                
                // Check for king attacks (adjacent squares)
                for (let dr = -1; dr <= 1; dr++) {
                    for (let dc = -1; dc <= 1; dc++) {
                        if (dr === 0 && dc === 0) continue;
                        
                        const newRow = row + dr;
                        const newCol = col + dc;
                        
                        if (isInBounds(newRow, newCol)) {
                            const piece = gameState.board[newRow][newCol];
                            if (piece && piece.color === opponentColor && piece.type === 'k') {
                                return true;
                            }
                        }
                    }
                }
                
                // Check for straight attacks (rook/queen)
                const straightDirections = [
                    { dr: 1, dc: 0 },  // down
                    { dr: -1, dc: 0 }, // up
                    { dr: 0, dc: 1 },  // right
                    { dr: 0, dc: -1 }  // left
                ];
                
                for (const dir of straightDirections) {
                    for (let i = 1; i < 8; i++) {
                        const newRow = row + dir.dr * i;
                        const newCol = col + dir.dc * i;
                        
                        if (!isInBounds(newRow, newCol)) break;
                        
                        const piece = gameState.board[newRow][newCol];
                        if (piece) {
                            if (piece.color === opponentColor && (piece.type === 'r' || piece.type === 'q')) {
                                return true;
                            }
                            break;
                        }
                    }
                }
                
                // Check for diagonal attacks (bishop/queen)
                const diagonalDirections = [
                    { dr: 1, dc: 1 },   // down-right
                    { dr: 1, dc: -1 },   // down-left
                    { dr: -1, dc: 1 },   // up-right
                    { dr: -1, dc: -1 }   // up-left
                ];
                
                for (const dir of diagonalDirections) {
                    for (let i = 1; i < 8; i++) {
                        const newRow = row + dir.dr * i;
                        const newCol = col + dir.dc * i;
                        
                        if (!isInBounds(newRow, newCol)) break;
                        
                        const piece = gameState.board[newRow][newCol];
                        if (piece) {
                            if (piece.color === opponentColor && (piece.type === 'b' || piece.type === 'q')) {
                                return true;
                            }
                            break;
                        }
                    }
                }
                
                return false;
            }

            // Check if a player has any legal moves
            function hasAnyLegalMoves(color) {
                for (let row = 0; row < 8; row++) {
                    for (let col = 0; col < 8; col++) {
                        const piece = gameState.board[row][col];
                        if (piece && piece.color === color) {
                            const moves = getValidMoves(row, col);
                            if (moves.length > 0) {
                                return true;
                            }
                        }
                    }
                }
                return false;
            }

            // Make computer move
            function makeComputerMove() {
                if (gameState.gameOver || gameState.turn !== 'black') return;
                
                // Get all possible moves
                const allMoves = [];
                for (let row = 0; row < 8; row++) {
                    for (let col = 0; col < 8; col++) {
                        const piece = gameState.board[row][col];
                        if (piece && piece.color === 'black') {
                            const moves = getValidMoves(row, col);
                            moves.forEach(move => {
                                allMoves.push({
                                    fromRow: row,
                                    fromCol: col,
                                    toRow: move.row,
                                    toCol: move.col,
                                    isCastle: move.isCastle,
                                    isEnPassant: move.isEnPassant
                                });
                            });
                        }
                    }
                }
                
                if (allMoves.length === 0) return;
                
                // Simple AI based on difficulty
                let move;
                if (gameState.computerDifficulty === 'easy') {
                    // Random move
                    move = allMoves[Math.floor(Math.random() * allMoves.length)];
                } else if (gameState.computerDifficulty === 'medium') {
                    // Prefer captures and checks
                    const capturingMoves = allMoves.filter(m => 
                        gameState.board[m.toRow][m.toCol] || 
                        isSquareAttacked(m.toRow, m.toCol, 'white')
                    );
                    
                    if (capturingMoves.length > 0) {
                        move = capturingMoves[Math.floor(Math.random() * capturingMoves.length)];
                    } else {
                        move = allMoves[Math.floor(Math.random() * allMoves.length)];
                    }
                } else { // hard
                    // Simple evaluation - prefer captures of higher value pieces
                    const pieceValues = { p: 1, n: 3, b: 3, r: 5, q: 9, k: 0 };
                    
                    const evaluatedMoves = allMoves.map(m => {
                        let score = 0;
                        const target = gameState.board[m.toRow][m.toCol];
                        
                        // Capture
                        if (target) {
                            score += pieceValues[target.type] * 10;
                        }
                        
                        // Check
                        const tempBoard = JSON.parse(JSON.stringify(gameState.board));
                        tempBoard[m.toRow][m.toCol] = tempBoard[m.fromRow][m.fromCol];
                        tempBoard[m.fromRow][m.fromCol] = null;
                        
                        if (isSquareAttacked(gameState.whiteKingPos.row, gameState.whiteKingPos.col, 'white')) {
                            score += 5;
                        }
                        
                        // Center control
                        if ((m.toRow >= 3 && m.toRow <= 4) && (m.toCol >= 3 && m.toCol <= 4)) {
                            score += 2;
                        }
                        
                        // Development (knights and bishops)
                        const piece = gameState.board[m.fromRow][m.fromCol];
                        if ((piece.type === 'n' || piece.type === 'b') && 
                            (m.fromRow === 0 || m.fromRow === 7) && 
                            (m.fromCol === 1 || m.fromCol === 6 || m.fromCol === 2 || m.fromCol === 5)) {
                            score += 1;
                        }
                        
                        return { move: m, score };
                    });
                    
                    // Sort by score
                    evaluatedMoves.sort((a, b) => b.score - a.score);
                    
                    // Pick one of the top 3 moves
                    const topMoves = evaluatedMoves.slice(0, 3);
                    move = topMoves[Math.floor(Math.random() * topMoves.length)].move;
                }
                
                // Make the move
                makeMove(move.fromRow, move.fromCol, move.toRow, move.toCol);
            }

            // Undo last move
            function undoLastMove() {
                if (gameState.moveHistory.length === 0 || gameState.promotionPending) return;
                
                // Reload from initial state
                const initialState = {
                    board: Array(8).fill().map(() => Array(8).fill(null)),
                    turn: 'white',
                    whiteKingPos: { row: 7, col: 4 },
                    blackKingPos: { row: 0, col: 4 },
                    whiteCanCastle: { kingside: true, queenside: true },
                    blackCanCastle: { kingside: true, queenside: true },
                    enPassantTarget: null,
                    halfmoveClock: 0,
                    fullmoveNumber: 1,
                    gameOver: false
                };
                
                // Replay all moves except the last one
                gameState.moveHistory.pop();
                resetGame();
                
                for (const moveNotation of gameState.moveHistory) {
                    // Simplified move parsing - in a real implementation, you'd need proper move parsing
                    // This is just a placeholder to demonstrate the concept
                    let moveFound = false;
                    
                    for (let fromRow = 0; fromRow < 8 && !moveFound; fromRow++) {
                        for (let fromCol = 0; fromCol < 8 && !moveFound; fromCol++) {
                            const piece = gameState.board[fromRow][fromCol];
                            if (!piece || piece.color !== gameState.turn) continue;
                            
                            const moves = getValidMoves(fromRow, fromCol);
                            for (const move of moves) {
                                // Very simplified move matching - doesn't handle all cases
                                const expectedNotation = 
                                    (piece.type === 'p' ? '' : piece.type.toUpperCase()) + 
                                    String.fromCharCode(97 + move.col) + 
                                    (8 - move.row);
                                
                                if (expectedNotation === moveNotation || 
                                    (moveNotation.startsWith('O-O') && move.isCastle)) {
                                    makeMove(fromRow, fromCol, move.row, move.col);
                                    moveFound = true;
                                    break;
                                }
                            }
                        }
                    }
                    
                    if (!moveFound) {
                        console.error("Could not parse move:", moveNotation);
                        resetGame();
                        return;
                    }
                }
                
                updateUI();
            }

            // Show hint
            function showHint() {
                if (gameState.gameOver || gameState.turn !== 'white' || gameState.vsComputer) return;
                
                // Clear previous hints
                gameState.validMoves = [];
                gameState.selectedSquare = null;
                updateBoardVisual();
                
                // Get all possible moves
                const allMoves = [];
                for (let row = 0; row < 8; row++) {
                    for (let col = 0; col < 8; col++) {
                        const piece = gameState.board[row][col];
                        if (piece && piece.color === 'white') {
                            const moves = getValidMoves(row, col);
                            moves.forEach(move => {
                                allMoves.push({
                                    fromRow: row,
                                    fromCol: col,
                                    toRow: move.row,
                                    toCol: move.col
                                });
                            });
                        }
                    }
                }
                
                if (allMoves.length === 0) return;
                
                // Pick a random move to suggest
                const hintMove = allMoves[Math.floor(Math.random() * allMoves.length)];
                
                // Highlight the suggested piece and move
                gameState.selectedSquare = { row: hintMove.fromRow, col: hintMove.fromCol };
                gameState.validMoves = [{ row: hintMove.toRow, col: hintMove.toCol }];
                updateBoardVisual();
            }

            // Event listeners
            newGameBtn.addEventListener('click', resetGame);
            undoBtn.addEventListener('click', undoLastMove);
            hintBtn.addEventListener('click', showHint);
            
            gameMode.addEventListener('change', () => {
                gameState.vsComputer = gameMode.value === 'computer';
                difficulty.style.display = gameState.vsComputer ? 'block' : 'none';
                resetGame();
            });
            
            difficulty.addEventListener('change', () => {
                gameState.computerDifficulty = difficulty.value;
            });
            
            promotionOptions.forEach(option => {
                option.addEventListener('click', () => {
                    const piece = option.dataset.piece;
                    promotionModal.style.display = 'none';
                    
                    if (gameState.promotionPending) {
                        const { fromRow, fromCol, toRow, toCol } = gameState.promotionPending;
                        makeMove(fromRow, fromCol, toRow, toCol, piece);
                        gameState.promotionPending = null;
                    }
                });
            });
            
            // Initialize the game
            initializeBoard();
        });
    </script>
</body>
</html>
