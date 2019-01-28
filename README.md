import chess 
board = chess.Board() 
import time 
from IPython.display import display, HTML, clear_output 

def who(player): 
    return "White" if player == chess.WHITE else "Black" 

def display_board(board, use_svg): 
    if use_svg: 
        return board._repr_svg_() 
    else: 
        return "<pre>" + str(board) + "</pre>" 

def play_game(player1, player2, visual="svg", pause=0.1): 
    """ 
    playerN1, player2: functions that takes board, return uci move 
    visual: "simple" | "svg" | None 
    """ 
    use_svg = (visual == "svg") 
    board = chess.Board() 
    try: 
        while not board.is_game_over(claim_draw=True): 
            if board.turn == chess.WHITE: 
                uci = player1(board) 
            else: 
                uci = player2(board) 
            name = who(board.turn) 
            board.push_uci(uci) 
            board_stop = display_board(board, use_svg) 
            html = "<b>Move %s %s, Play '%s':</b><br/>%s" % ( 
                       len(board.move_stack), name, uci, board_stop) 
            if visual is not None: 
                if visual == "svg": 
                    clear_output(wait=True) 
                display(HTML(html)) 
                if visual == "svg": 
                    time.sleep(pause) 
    except KeyboardInterrupt: 
        msg = "Game interrupted!" 
        return (None, msg, board) 
    result = None 
    if board.is_checkmate(): 
        msg = "checkmate: " + who(not board.turn) + " wins!" 
        result = not board.turn 
    elif board.is_stalemate(): 
        msg = "draw: stalemate" 
    elif board.is_fivefold_repetition(): 
        msg = "draw: 5-fold repetition" 
    elif board.is_insufficient_material(): 
        msg = "draw: insufficient material" 
    elif board.can_claim_draw(): 
        msg = "draw: claim" 
    if visual is not None: 
        print(msg) 
    return (result, msg, board) 

import random 

def random_player(board): 
    move = random.choice(list(board.legal_moves)) 
    return move.uci() 

def human_player(board): 
    display(board) 
    uci = get_move("%s's move [q to quit]> " % who(board.turn)) 
    legal_uci_moves = [move.uci() for move in board.legal_moves] 
    while uci not in legal_uci_moves: 
        print("Legal moves: " + (",".join(sorted(legal_uci_moves)))) 
        uci = get_move("%s's move[q to quit]> " % who(board.turn)) 
    return uci 

def get_move(prompt): 
    uci = input(prompt) 
    if uci and uci[0] == "q": 
        raise KeyboardInterrupt() 
    try: 
        chess.Move.from_uci(uci) 
    except: 
        uci = None 
    return uci 

def player1(board): 
    moves = list(board.legal_moves) 
    for move in moves: 
        newboard = board.copy() 
        # go through board and return a score 
        move.score = staticAnalysis1(newboard, move, board.turn) 
    moves.sort(key=lambda move: move.score, reverse=True) # sort on score 
    return moves[0].uci() 

def player2(board): 
    moves = list(board.legal_moves) 
    for move in moves: 
        newboard = board.copy() 
        # go through board and return a score 
        move.score = staticAnalysis2(newboard, move, board.turn) 
    moves.sort(key=lambda move: move.score, reverse=True) # sort on score 
    return moves[0].uci() 

def player3(board): 
    moves = list(board.legal_moves) 
    for move in moves: 
        newboard = board.copy() 
        # go through board and return a score 
        move.score = staticAnalysis3(newboard, move, board.turn) 
    moves.sort(key=lambda move: move.score, reverse=True) # sort on score 
    return moves[0].uci() 

def player4(board): 
    moves = list(board.legal_moves) 
    for move in moves: 
        newboard = board.copy() 
        # go through board and return a score 
        move.score = staticAnalysis4(newboard, move, board.turn) 
    moves.sort(key=lambda move: move.score, reverse=True) # sort on score 
    return moves[0].uci() 

def staticAnalysis1(board, move, my_color): 
    score = 0 
    ## Check some things about this move: 
    # score += 10 if board.is_capture(move) else 0 
    # To actually make the move: 
    board.push(move) 
    # Now check some other things: 
    for (piece, value) in [(chess.PAWN, 1),  
                           (chess.BISHOP, 4),  
                           (chess.KING, 0),  
                           (chess.QUEEN, 10),  
                           (chess.KNIGHT, 5), 
                           (chess.ROOK, 3)]: 
        score += len(board.pieces(piece, my_color)) * value 
        score -= len(board.pieces(piece, not my_color)) * value 
        # can also check things about the pieces position here 
    return score 

def staticAnalysis2(board, move, my_color):                              # mejora de la funcion de analisis anterior 
    score = random.random() 
    ## Check some things about this move: 
    # score += 10 if board.is_capture(move) else 0 
    # To actually make the move: 
    board.push(move) 
    # Now check some other things: 
    for (piece, value) in [(chess.PAWN, 1),  
                           (chess.BISHOP, 4),  
                           (chess.KING, 0),  
                           (chess.QUEEN, 10),  
                           (chess.KNIGHT, 5), 
                           (chess.ROOK, 3)]: 
        score += len(board.pieces(piece, my_color)) * value 
        score -= len(board.pieces(piece, not my_color)) * value 
        # can also check things about the pieces position here 
    return score 

def staticAnalysis3(board, move, my_color):                             # mejora de la funcion de analisis anterior 
    score = random.random() 
    ## Check some things about this move: 
    # score += 10 if board.is_capture(move) else 0 
    # To actually make the move: 
    board.push(move) 
    # Now check some other things: 
    for (piece, value) in [(chess.PAWN, 1),  
                           (chess.BISHOP, 4),  
                           (chess.KING, 0),  
                           (chess.QUEEN, 10),  
                           (chess.KNIGHT, 5), 
                           (chess.ROOK, 3)]: 
        score += len(board.pieces(piece, my_color)) * value 
        score -= len(board.pieces(piece, not my_color)) * value 
        # can also check things about the pieces position here 
    # Check global things about the board 
    score += 100 if board.is_checkmate() else 0 
    return score 

def staticAnalysis4(board, move, my_color):                             # mejora de la funcion de analisis anterior, pero no hace caso a la última mejora... 
    score = random.random() 
    ## Check some things about this move: 
    # score += 10 if board.is_capture(move) else 0 
    # To actually make the move: 
    board.push(move) 
    # Now check some other things: 
    for (piece, value) in [(chess.PAWN, 1),  
                           (chess.BISHOP, 4),  
                           (chess.KING, 0),  
                           (chess.QUEEN, 10),  
                           (chess.KNIGHT, 5), 
                           (chess.ROOK, 3)]: 
        score += len(board.pieces(piece, my_color)) * value 
        score -= len(board.pieces(piece, not my_color)) * value 
        # can also check things about the pieces position here 
    # Check global things about the board 
    score += 100 if board.is_checkmate() else 0 
    score += 1000 if board.is_castling(move) else 0   # No se por que no se acaba de enrocar... 
    score += 1 if board.is_zeroing(move) else 0 
    return score 

play_game(player4, player4) 