import tkinter as tk
from tkinter import filedialog, messagebox

# --- 함수 정의 ---

def new_file():
    """새 파일을 만듭니다."""
    text_area.delete(1.0, tk.END)
    window.title("제목 없음 - 메모장")

def open_file():
    """파일을 엽니다."""
    file_path = filedialog.askopenfilename(
        defaultextension=".txt",
        filetypes=[("텍스트 문서", "*.txt"), ("모든 파일", "*.*")]
    )
    if not file_path:
        return
    
    try:
        with open(file_path, "r", encoding="utf-8") as input_file:
            text = input_file.read()
            text_area.delete(1.0, tk.END)
            text_area.insert(tk.END, text)
        window.title(f"{file_path} - 메모장")
    except Exception as e:
        messagebox.showerror("오류", f"파일을 여는 중 오류가 발생했습니다: {e}")

def save_file():
    """파일을 저장합니다."""
    file_path = filedialog.asksaveasfilename(
        defaultextension=".txt",
        filetypes=[("텍스트 문서", "*.txt"), ("모든 파일", "*.*")]
    )
    if not file_path:
        return

    try:
        with open(file_path, "w", encoding="utf-8") as output_file:
            text = text_area.get(1.0, tk.END)
            output_file.write(text)
        window.title(f"{file_path} - 메모장")
    except Exception as e:
        messagebox.showerror("오류", f"파일을 저장하는 중 오류가 발생했습니다: {e}")

def exit_program():
    """프로그램을 종료합니다."""
    if messagebox.askokcancel("종료", "메모장을 종료하시겠습니까?"):
        window.destroy()

# --- 편집 기능 함수 ---
def undo():
    """실행 취소"""
    text_area.edit_undo()

def redo():
    """다시 실행"""
    text_area.edit_redo()

def cut():
    """잘라내기"""
    text_area.event_generate("<<Cut>>")

def copy():
    """복사하기"""
    text_area.event_generate("<<Copy>>")

def paste():
    """붙여넣기"""
    text_area.event_generate("<<Paste>>")

def select_all():
    """모두 선택"""
    text_area.tag_add("sel", "1.0", "end")
    return "break"

# --- GUI 설정 ---

# 1. 메인 윈도우 생성
window = tk.Tk()
window.title("제목 없음 - 메모장")
window.geometry("800x600")

# 2. 메뉴바 생성
menu_bar = tk.Menu(window)

# 3. '파일' 메뉴 생성
file_menu = tk.Menu(menu_bar, tearoff=0)
file_menu.add_command(label="새 파일", command=new_file)
file_menu.add_command(label="열기", command=open_file)
file_menu.add_command(label="저장", command=save_file)
file_menu.add_separator()
file_menu.add_command(label="종료", command=exit_program)
menu_bar.add_cascade(label="파일", menu=file_menu)

# 4. '편집' 메뉴 생성
edit_menu = tk.Menu(menu_bar, tearoff=0)
edit_menu.add_command(label="실행 취소", command=undo)
edit_menu.add_command(label="다시 실행", command=redo)
edit_menu.add_separator()
edit_menu.add_command(label="잘라내기", command=cut)
edit_menu.add_command(label="복사", command=copy)
edit_menu.add_command(label="붙여넣기", command=paste)
edit_menu.add_separator()
edit_menu.add_command(label="모두 선택", command=select_all)
menu_bar.add_cascade(label="편집", menu=edit_menu)

# 윈도우에 메뉴바 설정
window.config(menu=menu_bar)

# 5. 텍스트 입력 영역 생성 (undo 기능 활성화)
text_area = tk.Text(window, wrap=tk.WORD, undo=True)
text_area.pack(expand=True, fill="both")

# 6. 윈도우의 종료 버튼(X)을 눌렀을 때 exit_program 함수가 실행되도록 설정
window.protocol("WM_DELETE_WINDOW", exit_program)

# 7. 윈도우 실행
window.mainloop()
