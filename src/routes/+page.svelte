<script lang="ts">
    let board = Array(9).fill(null);
    let gameOver = false;
    let winner = null;

    function checkWinner(squares: (string | null)[]) {
        const lines = [
            [0, 1, 2],
            [3, 4, 5],
            [6, 7, 8],
            [0, 3, 6],
            [1, 4, 7],
            [2, 5, 8],
            [0, 4, 8],
            [2, 4, 6]
        ];

        for (const [a, b, c] of lines) {
            if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
                return squares[a];
            }
        }
        return null;
    }

    function findWinningMove(squares: (string | null)[], player: string): number | null {
        for (let i = 0; i < squares.length; i++) {
            if (!squares[i]) {
                const boardCopy = [...squares];
                boardCopy[i] = player;
                if (checkWinner(boardCopy) === player) {
                    return i;
                }
            }
        }
        return null;
    }

    function computerMove() {
        // 1. Check if computer can win
        const winningMove = findWinningMove(board, 'O');
        if (winningMove !== null) {
            board[winningMove] = 'O';
            board = [...board];
            checkGameStatus();
            return;
        }

        // 2. Block player's winning move
        const blockingMove = findWinningMove(board, 'X');
        if (blockingMove !== null) {
            board[blockingMove] = 'O';
            board = [...board];
            checkGameStatus();
            return;
        }

        // 3. Take center if available
        if (board[4] === null) {
            board[4] = 'O';
            board = [...board];
            checkGameStatus();
            return;
        }

        // 4. Take corners strategically
        const corners = [0, 2, 6, 8];
        const availableCorners = corners.filter(corner => board[corner] === null);
        
        // If player has opposite corners, take sides to prevent a trap
        if ((board[0] === 'X' && board[8] === 'X') || (board[2] === 'X' && board[6] === 'X')) {
            const sides = [1, 3, 5, 7];
            const availableSides = sides.filter(side => board[side] === null);
            if (availableSides.length > 0) {
                const randomSide = availableSides[Math.floor(Math.random() * availableSides.length)];
                board[randomSide] = 'O';
                board = [...board];
                checkGameStatus();
                return;
            }
        }

        // Take a corner if available
        if (availableCorners.length > 0) {
            const randomCorner = availableCorners[Math.floor(Math.random() * availableCorners.length)];
            board[randomCorner] = 'O';
            board = [...board];
            checkGameStatus();
            return;
        }

        // 5. Take any available spot
        const emptySpots = board.map((spot, index) => spot === null ? index : null).filter(spot => spot !== null);
        if (emptySpots.length > 0) {
            const randomSpot = emptySpots[Math.floor(Math.random() * emptySpots.length)];
            board[randomSpot] = 'O';
            board = [...board];
            checkGameStatus();
        }
    }

    function checkGameStatus() {
        const currentWinner = checkWinner(board);
        if (currentWinner) {
            winner = currentWinner;
            gameOver = true;
        } else if (!board.includes(null)) {
            gameOver = true;
        }
    }

    function handleClick(index: number) {
        if (board[index] || gameOver) return;

        board[index] = 'X';
        board = [...board];
        checkGameStatus();

        if (!gameOver) {
            setTimeout(computerMove, 500);
        }
    }

    function resetGame() {
        board = Array(9).fill(null);
        gameOver = false;
        winner = null;
    }
</script>

<div class="min-h-screen bg-gray-100 py-8">
    <div class="max-w-md mx-auto bg-white rounded-xl shadow-md overflow-hidden p-6">
        <h1 class="text-3xl font-bold text-center mb-6 text-gray-800">Tic Tac Toe</h1>
        
        <div class="grid grid-cols-3 gap-2 mb-6">
            {#each board as cell, i}
                <button
                    on:click={() => handleClick(i)}
                    class="w-24 h-24 bg-gray-50 hover:bg-gray-100 border border-gray-200 rounded-lg text-4xl font-bold flex items-center justify-center transition-colors duration-200"
                    disabled={gameOver}
                >
                    {#if cell === 'X'}
                        <span class="text-blue-600">X</span>
                    {:else if cell === 'O'}
                        <span class="text-red-600">O</span>
                    {/if}
                </button>
            {/each}
        </div>

        {#if gameOver}
            <div class="text-center mb-4">
                {#if winner}
                    <p class="text-xl font-semibold text-gray-800">
                        {winner === 'X' ? 'You won!' : 'Computer won!'}
                    </p>
                {:else}
                    <p class="text-xl font-semibold text-gray-800">It's a draw!</p>
                {/if}
            </div>
        {/if}

        <button
            on:click={resetGame}
            class="w-full py-2 px-4 bg-blue-600 text-white font-semibold rounded-lg hover:bg-blue-700 transition-colors duration-200"
        >
            New Game
        </button>
    </div>
</div>
