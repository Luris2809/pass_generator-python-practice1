from tkinter import *
from tkinter import ttk
import datetime
import csv
import string
import random
import pandas as pd 
import glob
import os
# 1. ウィンドウを作成　　名前付け　サイズ変更
root = Tk()
root.title("パスワードジェネレーター")
root.geometry("500x500")
now_str = datetime.datetime.now().strftime('%H:%M:%S.%f')
today_str = datetime.datetime.today().strftime('%Y/%m/%d')
#


# パスワードに使う文字セット
def generate_password(length):
    lowercase = list(string.ascii_lowercase)
    uppercase = list(string.ascii_uppercase)
    digits = list(string.digits)
    punctuation = list(string.punctuation)
    all_chars = lowercase + uppercase + digits + punctuation

    password = ''.join(random.choice(all_chars) for _ in range(length)) 
    return password
    
def csv_copy_copy():
    # 日時の取得
    now = datetime.datetime.now()
    # today = datetime.datetime.today()
    # # ディレクトリの指定はここ
    # filename = './output/log_' + now.strftime('%Y%m%d_%H%M%S') + '.csv'
    """すべてのパスワードをCSVファイルに保存"""
    content = result_text.get(1.0, END).strip()
    if content:
        passwords = []
        # すべての行からパスワードを抽出
        for line in content.split('\n'):
            if "生成されたパスワード:" in line:
                password = line.split("生成されたパスワード: ")[1]
                passwords.append(password)
        
        password_len_str = str(len(password))

        if passwords:
            with open('passwords.csv', 'w', newline='') as f:
                writer = csv.writer(f)
                writer.writerow(['today','time','Password','len'])  # ヘッダー行
                for password in passwords:
                    writer.writerow([password,today_str,now_str,password_len_str])
            with open('passwords.csv', 'a', newline='') as f:
                writer = csv.writer(f)
                for password in passwords:
                    writer.writerow([password,today_str,now_str])
            result_text.insert(END, f"{len(passwords)}個のパスワードをCSVに保存しました\n")
        else:
            result_text.insert(END, "保存するパスワードがありません\n")
    else:
        result_text.insert(END, "保存するパスワードがありません\n")

def analyze_csv():
    """保存されたCSVを読み込み・整形・統合・分析する"""

    # 1️⃣ 保存フォルダ内のCSVファイルを全部読み込み
    files = glob.glob("./output/*.csv")
    if not files:
        result_text.insert(END, "CSVファイルが見つかりませんでした。\n")
        return

    # 2️⃣ すべてのCSVをDataFrameに読み込み → 結合
    df_list = [pd.read_csv(file) for file in files]
    df = pd.concat(df_list, ignore_index=True)

    # 3️⃣ 整形処理：重複削除・並べ替えなど
    df.drop_duplicates(subset=["Password"], inplace=True)
    df.sort_values(by=["today", "time"], ascending=False, inplace=True)

    # 4️⃣ パスワード長の平均などの簡易統計を追加
    df["len"] = df["Password"].astype(str).apply(len)
    avg_len = df["len"].mean()
    unique_count = df["Password"].nunique()

    # 5️⃣ 結果をファイル出力
    output_file = "./output/passwords_summary.csv"
    df.to_csv(output_file, index=False)

    result_text.insert(
        END,
        f"📊 CSVを統合しました: {len(df)}件\n"
        f"平均パスワード長: {avg_len:.2f}\n"
        f"重複なしの件数: {unique_count}\n"
        f"保存先: {output_file}\n"
    )


# 2. 関数を定義
def click():
    length_text = entry.get()
    if length_text.strip() == "" :
        result_text.insert(END, "数字を入力してください\n")
        return
    
    # 数字に変換（エラーハンドリング付き）
    try:
        length = int(length_text)
        if 1 <= length <= 100:  # 適切な範囲をチェック
            password = generate_password(length)
            result_text.insert(END, f"生成されたパスワード: {password}\n")
        else:
            result_text.insert(END, "1から100の間で入力してください\n")
    except ValueError:
        result_text.insert(END, "数字を入力してください\n")

def reload_program():
    """プログラムを再読み込みする"""
    import subprocess
    import sys
    
    # 現在のプログラムを再実行
    subprocess.Popen([sys.executable, __file__])
    root.quit()  # 現在のウィンドウを閉じる
def copy_to_clipboard():
    """生成されたパスワードをクリップボードにコピー"""
    # テキストエリアから最後の行を取得
    content = result_text.get(1.0, END).strip()
    if content:
        # 最後の行を取得（最新のパスワード）
        lines = content.split('\n')
        last_line = lines[-1] if lines else ""
        
        # パスワード部分を抽出
        if "生成されたパスワード:" in last_line:
            password = last_line.split("生成されたパスワード: ")[1]
            root.clipboard_clear()
            root.clipboard_append(password)
            result_text.insert(END, f"コピーしました: {password}\n")
        else:
            result_text.insert(END, "コピーするパスワードがありません\n")
    else:
        result_text.insert(END, "コピーするパスワードがありません\n")
csv_button = Button(root, text="CSV生成", command=csv_copy_copy)
csv_button.place(x=100, y=350)


copy_button = Button(root, text="コピー", command=copy_to_clipboard)
copy_button.place(x=200, y=350)

# 結果表示用のテキストエリアを追加
result_text = Text(root, height=20, width=62)
result_text.place(x=30, y=60)
# 3. ウィジェットを作成（rootを親として指定）
entry = Entry(root)
entry.place(x=30, y=30)

analyze_button = Button(root, text="📊 CSV統合", command=analyze_csv)
analyze_button.place(x=300, y=400)

# リロードボタンを追加
reload_button = Button(root, text="プログラム再読み込み", command=reload_program)
reload_button.place(x=300, y=350)

button = Button(root, text="生成", command=click)
button.place(x=220, y=26)

# 終了ボタンを追加する場合
exit_button = Button(root, text="終了", command=root.quit)
exit_button.place(x=330, y=26)

# クリアボタンを追加
clear_button = Button(root, text="クリア", command=lambda: result_text.delete(1.0, END))
clear_button.place(x=400, y=26)

# プレースホルダーを設定
entry.insert(0, "パスワードの長さを入力")
entry.config(fg='gray')

def on_focus_in(event):
    if entry.get() == "パスワードの長さを入力":
        entry.delete(0, END)
        entry.config(fg='white')

def on_focus_out(event):
    if entry.get() == "":
        entry.insert(0, "パスワードの長さを入力")
        entry.config(fg='gray')

entry.bind('<FocusIn>', on_focus_in)
entry.bind('<FocusOut>', on_focus_out)
def on_return_key(event):
    click()
# EntryにEnterキーをバインド
entry.bind('<Return>', on_return_key)
entry.bind('<Return>', on_return_key)  
# 4. メインループ
root.mainloop()



