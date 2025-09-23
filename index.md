## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/maxpermal/maxpermal.github.io/edit/master/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/maxpermal/maxpermal.github.io/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
# pythonProject

# src

## src\_version.py

````python
__version__ = "0.14.0"

````

## src\detect_text_from_frame.py

````python
import csv
import os
import re
import sys
import time
from concurrent.futures import ThreadPoolExecutor, as_completed
from logging import Logger
from typing import Callable, TextIO
from typing import List
from datetime import datetime

import cv2
import pytesseract

from logs import init_logger, __print_info, __print_erreur

# ──────────────────────────────
# si traitement via executable pyinstaller
# Définir ici le chemin de Tesseract si ce n'est pas dans le PATH
# Exemple Windows :
# pytesseract.pytesseract.tesseract_cmd = r"C:\Program Files\Tesseract-OCR\tesseract.exe"
if hasattr(sys, '_MEIPASS'):
    tesseract_path = os.path.join(sys._MEIPASS, 'Tesseract-OCR', 'tesseract.exe')
else:
    tesseract_path = r"C:\Program Files\Tesseract-OCR\tesseract.exe"
pytesseract.pytesseract.tesseract_cmd = tesseract_path
# ──────────────────────────────

mylog: Logger


def normalize_ocr_line(raw: str) -> str:
    """
    Normalise une trame OCR Tesseract au format :
    'YYYY-MM-DD HH:MM:SS xxxx.xxxkm yyykm/h'
    """

    # 1) Nettoyage brut
    txt = raw.strip()
    txt = txt.replace("O", "0").replace("o", "0").replace("?", "")
    txt = re.sub(r"\s+", " ", txt)

    # 2) Corriger séparateurs possibles
    txt = re.sub(r"(\d{2})[.;](\d{2})[.;](\d{2})", r"\1:\2:\3", txt)  # ex: 11.27.36 → 11:27:36

    # 3) Extraire date
    match_date = re.search(r"(\d{4}-\d{2}-\d{2})", txt)
    date_str = match_date.group(1) if match_date else "0000-00-00"

    # 4) Extraire heure
    match_time = re.search(r"(\d{2}:\d{2}:\d{2})", txt)
    time_str = match_time.group(1) if match_time else "00:00:00"

    # 5) Extraire distance
    match_dist = re.search(r"(\d{3,5})\s*\.?\s*(\d{3})\s*km", txt)
    if match_dist:
        dist_str = f"{int(match_dist.group(1)):04d}.{match_dist.group(2)}km"
    else:
        dist_str = "0000.000km"

    # 6) Extraire vitesse
    match_speed = re.search(r"(\d{2,3})\s*km/h", txt)
    if match_speed:
        speed_str = f"{int(match_speed.group(1)):03d}km/h"
    else:
        speed_str = "000km/h"

    # 7) Reconstruction
    return f"{date_str} {time_str} {dist_str} {speed_str}"


def detect_text_from_frame(img_path: str) -> str:
    """
    cherche et retourne le texte dans le cadre {0;0;560;30} d'une image
    :param img_path: nom de fichier complet
    :return: le texte
    """
    if not os.path.exists(img_path):
        __print_erreur(f"#Erreur: Fichier introuvable : {img_path}")
        return ""

    # Charger l'image
    image = cv2.imread(img_path)
    if image is None:
        __print_erreur("#Erreur: Impossible de charger l'image.")
        return ""

    # Extraire la zone (0,0) → (560x30)
    h, w = image.shape[:2]
    if w < 560 or h < 30:
        __print_erreur("#Erreur: L'image est trop petite pour contenir le cadre.")
        return ""
    roi = image[0:30, 0:560]

    # Passage en niveaux de gris
    gray: cv2.UMat = cv2.cvtColor(roi, cv2.COLOR_BGR2GRAY)

    # Amélioration du contraste (optionnel)
    _, thresh = cv2.threshold(gray, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)

    # OCR
    config = '--psm 7'  # Ligne unique
    text = pytesseract.image_to_string(thresh, config=config)
    text = text.strip()
    text_corrige = normalize_ocr_line(text)

    return text_corrige


def process_on_images_from_directory_parallel(directory_path: str, type_image: str,
                                              cb_avancement: Callable[[str], None] = None,
                                              max_workers: int = 4) -> List[str]:
    """
    Parcourt un répertoire, traite les images de 'type type_image' en parallèle avec ThreadPoolExecutor,
    extrait le texte des images et fait correspondre les distances et refait le ME0.
    :param max_workers: nombre de threads
    :param cb_avancement: callback avancement
    :param directory_path: le répertoire à chercher les images
    :param type_image: le type d'image ".jpg" ou ".bmp"
    :param cb_avancement: callback avancement (appelé dans le thread principal)
    """
    results: List[str] = []  # [ <filepath0>\t<text0>, <filepath1>\t<text1>, ...]
    count: int = 0

    # Liste des fichiers à traiter
    lst_dir = [f for f in os.listdir(directory_path) if f.lower().endswith(type_image.lower())]
    total_files = len(lst_dir)

    def process_one_file(filename):
        filepath_ = os.path.join(directory_path, filename)
        text_ = detect_text_from_frame(filepath_)
        __print_info(f"{filename} : {text_}")
        return filepath_, text_

    # passe les fichiers dans le pool de threads
    with ThreadPoolExecutor(max_workers=max_workers) as executor:
        futures = {executor.submit(process_one_file, f): idx for idx, f in enumerate(lst_dir)}
        _results_with_index = []  # ((ind, 'chemin fichier image', 'tag image'), ...)

        for future in as_completed(futures):
            idx_original = futures[future]  # récupérer l'index dans lst_dir
            filepath, text = future.result()
            _results_with_index.append((idx_original, filepath, text))
            count += 1
            if cb_avancement:
                cb_avancement(f"{total_files}\t{count}\t{filepath}\t{text}")

        _results_with_index.sort(key=lambda x: x[0])
        # results = []

        for idx_original, filepath, text in _results_with_index:
            results.append(f"{filepath}\t{text}")

    return results


def process_update_me0(dossier_image: str, list_fic_images: [], pas_acq: int, fichier_de_sortie_SAG: str,
                       fichier_de_sortie_SES: str,
                       fichier_locapo: str):
    """
    traite la liste des fichiers image, fait correspondre les distances, maj le ME0 SAG
    extrait les données SES d'une ligne, fait interpolation barycentrique pour avoir la position GPS
     maj le ME0 SES
    :param list_fic_images: liste des fichiers images traitées
    :param pas_acq: pas de l'acquisition entre les prises d'image
    :param fichier_de_sortie_SAG: fichier ME0 SAG
    :param fichier_de_sortie_SES: fichier ME0 SES
    :param fichier_locapo: ficher MEO locAPO
    :return: None
    """
    tacq: float = -1
    pas_acq_dernier: int = -1

    __print_info("Met à jour les fichiers ME0")

    if list_fic_images is None:
        __print_erreur('#Erreur La liste des résultats est vide.')
        return

    # Lecture du fichier locapo
    gps_data: list[dict[str, float]] = extract_gps_datas(fichier_locapo)

    # écriture dans fichier ME0
    fichier_de_sortie_ren: str = dossier_image + "/renommage.txt"
    f_ren: TextIO
    f_me0: TextIO
    f_ses: TextIO
    if os.path.exists(fichier_de_sortie_ren):
        os.rename(fichier_de_sortie_ren, f"{fichier_de_sortie_ren}_{datetime.now().strftime('%Y%m%d_%H%M%S')}.bak")
    with open(fichier_de_sortie_ren, "w+", encoding="utf-8") as f_ren, \
            open(fichier_de_sortie_SAG, "w+", encoding="utf-8") as f_me0, \
            open(fichier_de_sortie_SES, "w+", encoding="utf-8") as f_ses:

        f_me0.write("DIST(m)\tN°image\tFichier image\tt(s)\n")
        f_ses.write("ABD\tXDEB\tYDEB\tZDEB\tN°image\tFichier image\tt(s)\n")

        lignes: list[str] = []
        for ligne in list_fic_images:
            tacq, pas_acq_dernier, ligne_formatee = extract_tags_from_image_and_update_me0_sag(
                f_me0, f_ren, ligne, pas_acq, tacq, pas_acq_dernier
            )
            lignes.append(ligne_formatee)

            extract_token_from_line_and_update_me0_ses(f_ses, ligne_formatee, gps_data)


def extract_gps_datas(fichier_locapo):
    gps_data: list[dict[str, float]] = []
    f_loc: TextIO
    with open(fichier_locapo, "r", encoding="utf-8") as f_loc:
        reader = csv.DictReader(f_loc, delimiter="\t")
        for row in reader:
            gps_data.append({
                "ABD": float(row["ABD"]),
                "XDEB": float(row["XDEB"]),
                "YDEB": float(row["YDEB"]),
                "ZDEB": float(row["ZDEB"])
            })
    return gps_data


def extract_tags_from_image_and_update_me0_sag(f_me0: TextIO, f_renommage: TextIO, fic_image_texte: str, pas_acq: int,
                                               tacq: float, pas_acq_dernier: int) -> tuple[float, int, str]:
    """
    *interne* traite le fichier image, fait correspondre les distances, maj le ME0 SAG
    :param f_me0: handle de fichier me0
    :param f_renommage: hdndle de fichier pour notifier les fichiers images qui seront renommés
    :param fic_image_texte: <fichier image>tab<texte>
    :param pas_acq: pas d'acquisition
    :param tacq: var temp
    :param pas_acq_dernier: var temp
    :return: tuple[float, int, str] tacq, pas_acq_dernier, ligne ME0
    """
    nom_fichier_complet: str
    texte_detecte: str
    nom_fichier_complet, texte_detecte = fic_image_texte.split('\t', 1)
    nouveau_nom_complet: str = nom_fichier_complet
    ltoks: List[str] = nom_fichier_complet.split(".")

    # --- Extraction de la distance ---
    match_dist = re.search(r"(\d+\.\d{3})km", texte_detecte)
    if not match_dist:
        __print_erreur(f"erreur d'extraction de la distance dans le texte {texte_detecte}")
        return tacq, pas_acq_dernier, ""
    distance_km = float(match_dist.group(1))
    distance_m = round(distance_km * 1000, 3)

    # --- Extraction du temps hh:mm:ss ---
    match_time = re.search(r"(\d{2}):(\d{2}):(\d{2})", texte_detecte)
    if not match_time:
        __print_erreur(f"erreur d'extraction de l'heure dans le texte {texte_detecte}")
        return tacq, pas_acq_dernier, ""
    h, m, s = map(int, match_time.groups())
    temps_sec = h * 3600 + m * 60 + s
    if tacq == -1:
        tacq = temps_sec
    temps_sec = temps_sec - tacq

    # --- Extraction du n° d'image ---
    match_num = re.search(r"\.(\d{6})\.", nom_fichier_complet, re.IGNORECASE)
    if not match_num:
        __print_erreur(f"erreur d'extraction de num dans le texte {nom_fichier_complet}")
        return tacq, pas_acq_dernier, ""
    numero_image: int = int(match_num.group(1).lstrip('0') or "0")

    # --- Recale le nom du fichier avec la distance et renomme l'image en .JPG_---
    if abs(distance_m - numero_image * pas_acq) >= pas_acq:
        if pas_acq_dernier != pas_acq:
            pas_acq_dernier = pas_acq
            __print_info(f"====> renommage des fichiers à partir de {distance_m}")
        numero_image = int(distance_m / pas_acq)
        # APOIAE02U1250320.ME0_\APOIAE02U1250320.ME0_Image.1.000001.JPG
        #                       0                1         2 3      4
        nouveau_nom_complet = f"{'.'.join(ltoks[:-2])}.{numero_image:06d}.{ltoks[-1]}"
        f_renommage.write(f"{nom_fichier_complet}\t{nouveau_nom_complet}_\n")
        os.rename(nom_fichier_complet, nouveau_nom_complet + "_")

    # --- Écriture de la ligne dans ME0 SAG ---
    # "C:\IAE02U150320\APOIAE02U1250320.ME0_\APOIAE02U1250320.ME0_Image.1.000001.JPG"
    _nomfichier: str = os.path.basename(nouveau_nom_complet)  # 'APOIAE02U1250320.ME0_Image.1.000001.JPG'
    _nomdossier: str = os.path.basename(os.path.dirname(nouveau_nom_complet))  # 'APOIAE02U1250320.ME0_'
    _cheminrelatif: str = os.path.join(_nomdossier, _nomfichier)
    line: str = f"{distance_m}\t{numero_image:06d}\t{_cheminrelatif}\t{temps_sec}\n"
    f_me0.write(line)

    return tacq, pas_acq_dernier, line


def extract_token_from_line_and_update_me0_ses(f_ses: TextIO, ligne_formatee: str, gps_data: list[dict[str, float]]):
    """
    *interne* extrait les données SES d'une ligne, fait interpolation barycentrique pour avoir la position GPS
     maj le ME0 SES
    :param f_ses: handle fichier
    :param ligne_formatee: format attendu : <abscisse>\t<index image>\t<fichier image>\t<horodatage seconde>
    :param gps_data: liste des coordonnées GPS locAPO
    :return: None
    """
    # --- Extraction des champs ligne_formatee ---
    # format attendu : <abscisse>\t<index image>\t<fichier image>\t<horodatage seconde>
    parts: list[str] = ligne_formatee.strip().split("\t")
    if len(parts) != 4:
        __print_erreur(f"Erreur dans la ligne {ligne_formatee}")
        return
    abd: float = float(parts[0])
    num_image: str = parts[1]
    fichier_image: str = parts[2]
    t_sec: str = parts[3]

    # --- Interpolation barycentrique des coordonnées GPS ---
    x, y, z = interpolate_gps(abd, gps_data)

    # --- Écriture dans fichier ME0 SES ---
    ligne_ses: str = f"{abd:.3f}\t{x:.3f}\t{y:.3f}\t{z:.3f}\t{num_image}\t{fichier_image}\t{t_sec}\n"
    f_ses.write(ligne_ses)


def interpolate_gps(abd: float, gps_data: list) -> tuple[float, float, float]:
    """
    Interpolation barycentrique entre deux points GPS les plus proches pour obtenir XDEB, YDEB, ZDEB à ABD.
    :return: tuple[float, float, float] x,y,z
    :param abd: l'abscisse
    :param gps_data: list des coordonnées gps
    """
    # Si ABD est en dehors des bornes, on prend les extrêmes
    if abd <= gps_data[0]["ABD"]:
        return gps_data[0]["XDEB"], gps_data[0]["YDEB"], gps_data[0]["ZDEB"]
    if abd >= gps_data[-1]["ABD"]:
        return gps_data[-1]["XDEB"], gps_data[-1]["YDEB"], gps_data[-1]["ZDEB"]

    # Recherche des points encadrants
    for i in range(len(gps_data) - 1):
        abd0, abd1 = gps_data[i]["ABD"], gps_data[i + 1]["ABD"]
        if abd0 <= abd <= abd1:
            ratio = (abd - abd0) / (abd1 - abd0)
            x = gps_data[i]["XDEB"] + ratio * (gps_data[i + 1]["XDEB"] - gps_data[i]["XDEB"])
            y = gps_data[i]["YDEB"] + ratio * (gps_data[i + 1]["YDEB"] - gps_data[i]["YDEB"])
            z = gps_data[i]["ZDEB"] + ratio * (gps_data[i + 1]["ZDEB"] - gps_data[i]["ZDEB"])
            return x, y, z

    return gps_data[-1]["XDEB"], gps_data[-1]["YDEB"], gps_data[-1]["ZDEB"]


def process_rename_images_from_directory(directory_path: str, type_image: str) -> int:
    """
    Renomme les fichiers se terminant par "_" du précédent traitement
    :param directory_path: le répertoire
    :param type_image: le format d'image
    """
    __print_info(f"Renomme les fichiers image dans le dossier {directory_path}")
    count: int = 0
    for filename in os.listdir(directory_path):
        if filename.lower().endswith(type_image.lower() + "_"):
            filepath = os.path.join(directory_path, filename)
            __print_info(f"Renomme le fichier {filename} en {filepath[:-1]}")
            os.rename(filepath, filepath[:-1])
            count += 1
    return count


def process_tags_on_image_and_update_me0(directory_path: str, type_image: str, pas_acq: int, fichier_de_sortie_SAG: str,
                                         fichier_de_sortie_SES: str,
                                         fic_locapo: str, cb_avancement: Callable[[str], None] = None,
                                         max_workers: int = 4):
    _dossier_image: str = directory_path
    _extension_image: str = type_image
    _nom_fichier_sortie_me0_sag: str = fichier_de_sortie_SAG
    _nom_fichier_sortie_me0_ses: str = fichier_de_sortie_SES
    _fichier_entree_locapo: str = fic_locapo
    _pas_acquisition: int = pas_acq

    __print_info(f"\nTraitement de la session IAE.\n")
    start_time: float = time.time()

    result: list[str] = []
    fic_res_txt = _dossier_image + "/tags.txt"

    # traitement du dossier, écris les résultats dans un fichier txt
    if not os.path.exists(fic_res_txt):
        __print_info(f"\nTraitement du dossier image : {_dossier_image}.\n")
        result = process_on_images_from_directory_parallel(directory_path=_dossier_image,
                                                           type_image=_extension_image,
                                                           cb_avancement=cb_avancement,
                                                           max_workers=max_workers)
        end_time: float = time.time()
        elapsed: float = end_time - start_time
        __print_info(f"\n\t---> Temps de traitement des images : {elapsed:.2f} secondes pour {len(result)}fichiers.\n")

        __print_info(f"Ecris les résultats dans un fichier txt")
        with open(fic_res_txt, "w+", encoding="utf-8") as f_res:
            f_res.writelines(line + "\n" for line in result)

    # dossier déjà traité
    else:
        __print_info(f"\nImages déjà traitées dans le dossier {_dossier_image} : chargement du fichier...\n")
        with open(fic_res_txt, "r", encoding="utf-8") as f_res:
            result = f_res.read().splitlines()

    # maj MEO et renommage des images
    __print_info(f"\nMise à jour des fichiers ME0 et renommage des images.\n")
    process_update_me0(dossier_image=_dossier_image, list_fic_images=result, pas_acq=10,
                       fichier_de_sortie_SAG=_nom_fichier_sortie_me0_sag,
                       fichier_de_sortie_SES=_nom_fichier_sortie_me0_ses, fichier_locapo=_fichier_entree_locapo)

    process_rename_images_from_directory(_dossier_image, _extension_image)

    end_time = time.time()
    elapsed = end_time - start_time
    __print_info(f"\nTemps total du traitement : {elapsed:.2f} secondes pour {len(result)} fichiers.\n")

    fic_ren = _dossier_image + "/renommage.txt"
    with open(fic_ren, "r") as f:
        nb_image_ren = len(f.read().splitlines())

    return len(result), nb_image_ren


if __name__ == "__main__":
    dossier_image: str = "D:/tmp/iea02/IAE02U1250320/APOIAE02U1250320.ME0_"
    extension_image: str = ".jpg"
    nom_fichier_sortie_me0_sag: str = "d:/tmp/APOIAE02U1250320_SAG.ME0"
    nom_fichier_sortie_me0_ses: str = "d:/tmp/APOIAE02U1250320_SES.ME0"
    fichier_entree_locapo: str = "D:/tmp/iea02/APO01U1250320.SES/APOLOC03U1250320.ME0"
    pas_acquisition: int = 10

    # --- Exemples ---
    init_logger("d:/tmp/APOIAE02U1250320")
    process_tags_on_image_and_update_me0(dossier_image, extension_image, pas_acquisition, nom_fichier_sortie_me0_sag,
                                         nom_fichier_sortie_me0_ses, fichier_entree_locapo, None, os.cpu_count() * 4)

````

## src\logs.py

````python
import logging
import sys
from datetime import datetime
from logging import Logger

# le logger
log: logging.Logger = None
log_filename: str


def reset_logger():
    for handler in logging.root.handlers[:]:
        logging.root.removeHandler(handler)


def init_logger(nom_fichier_sortie_me0, logging_level: int = logging.INFO, console: bool = True):
    global log
    global log_filename

    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    log_filename = f"{nom_fichier_sortie_me0}_{timestamp}.log"
    handlers = [logging.FileHandler(log_filename, mode='a', encoding='utf-8')]
    if console:
        handlers.append(logging.StreamHandler(sys.stdout))
    logging.basicConfig(
        level=logging_level,
        format='[%(asctime)s] %(message)s',
        handlers=handlers
    )
    log = logging.getLogger()


def get_logfilename() -> str:
    return log_filename


def __print_debug(*args, **kwargs):
    global log
    if log:
        log.debug(*args, **kwargs)
    else:
        print(*args, **kwargs)


def __print_info(*args, **kwargs):
    global log
    if log:
        log.info(*args, **kwargs)
    else:
        print(*args, **kwargs)


def __print_erreur(*args, **kwargs):
    global log
    if log:
        log.error(*args, **kwargs)
    else:
        print(*args, **kwargs)

````

## src\main.py

````python
import os
import sys
from detect_text_from_frame import process_tags_on_image_and_update_me0
from logs import init_logger

# ----------------------------
# Utilisation: corrige_me0_iae02_v2.22.exe <dossier_image> <extension_image> <pas_acquisition>
#                               <nom_fichier_sortie_me0_SAG> <nom_fichier_sortie_me0_SES> <nom_fichier_entree_apoloc>
#                               <nom_base_log>
# ----------------------------

start_time: float = 0.0
end_time: float = 0.0
elapsed: float = 0.0

if len(sys.argv) < 7:
    # <nom_complet_dossier_image> <extension_image> <pas_acquisition_m0> <nom_complet_fichier_sortie_me0_SAG>
    # <nom_complet_fichier_sortie_me0_SES> <nom_complet_fichier_entree_apoloc_SES> <nom_complet_base_log>
    print(
        f"Utilisation: {sys.argv[0]} <nom_complet_dossier_image c:/.../images> <extension_image '.jpg'>"
        " <pas_acquisition_m 10> <nom_complet_fichier_sortie_me0_SAG c:/...SAG/session.MEO>"
        " <nom_complet_fichier_sortie_me0_SES c:/...SES/session.ME0>"
        " <nom_complet_fichier_entree_apoloc_SES c:/....SES/APOxxx.MEO> <nom_complet_base_log c:/.../monlog>")
    _dossier_image = "D:/tmp/iea02/APOIAE02U1250320.ME0_"
    _extension_image = ".jpg"
    _nom_fichier_sortie_me0_SAG = "d:/tmp/APOIAE02U1250320_SAG.ME0"
    _pas_acquisition: int = 10
    nom_fichier_sortie_me0_SES = "d:/tmp/APOIAE02U1250320_SES.ME0"
    nom_fichier_entree_apoloc = "D:/tmp/iea02/APO01U1250320.SES/APOLOC03U1250320.ME0"
    nom_base_log: str = "D:/tmp/iea02/APOIAE02U1250320"
else:
    _dossier_image = sys.argv[1]
    _extension_image = sys.argv[2].lower()
    _pas_acquisition: int = int(sys.argv[3])
    _nom_fichier_sortie_me0_SAG = sys.argv[4]
    nom_fichier_sortie_me0_SES: str = sys.argv[5]
    nom_fichier_entree_apoloc: str = sys.argv[6]
    nom_base_log: str = sys.argv[7]

    init_logger(nom_base_log)
    process_tags_on_image_and_update_me0(_dossier_image, _extension_image, _pas_acquisition,
                                         _nom_fichier_sortie_me0_SAG,
                                         nom_fichier_sortie_me0_SES, nom_fichier_entree_apoloc, None,
                                         os.cpu_count() * 4)

````

## src\main_disk.py

````python
import os
import sys
from pathlib import Path

from process_disk import process_disk
from rollback_session import rollback
from _version import __version__


def main():
    # Cas 1 : afficher la version
    if "--version" in sys.argv:
        print(__version__)
        sys.exit(0)

    # Pas assez d’arguments
    if len(sys.argv) < 2:
        print(f"Usage: {sys.argv[0]} <dossier racine des disques A3D>")
        print(f"Usage: {sys.argv[0]} rollback <dossier_AAAAMMJJ_A3D_Y_T_G> <session_ssssss>")
        sys.exit(1)

    # Cas 2 : rollback
    if sys.argv[1] == "rollback":
        if len(sys.argv) != 4:
            print(f"Usage: {sys.argv[0]} rollback <dossier_AAAAMMJJ_A3D_Y_T_G> <session_ssssss>")
            sys.exit(2)
        disque = sys.argv[2]
        session = sys.argv[3]
        rollback(disque, session)

    # Cas 3 : traitement standard
    else:
        base_dir = sys.argv[1]
        process_disk(base_dir, None, os.cpu_count()*4)


if __name__ == "__main__":
    main()

````

## src\main_disk_old.py

````python
import os
import re
import sys
import subprocess
from typing import Callable
from datetime import datetime
from logs import __print_info, __print_erreur, init_logger


def process_disk(base_dir: str, cb_avancement: Callable[[str], None] = None, max_workers: int = 4):
    __print_info("Recherche des disques A3D...")
    pattern = re.compile(r"^\d{8}_A3D_\d_[A-Z]_[A-Z]$")
    liste_disque_aigle = [
        os.path.join(base_dir, d)
        for d in os.listdir(base_dir)
        if os.path.isdir(os.path.join(base_dir, d)) and pattern.match(d)
    ]

    if len(liste_disque_aigle) <= 0:
        __print_erreur(f"Aucun disques A3D trouvés dans le répertoire {base_dir}")
        return

    __print_info(f"Disques trouvés :{''.join(liste_disque_aigle)}")

    for disque in liste_disque_aigle:

        __print_info(f"Traitement sur le disque {disque}")

        liste_session = list_sessions_from_disk(disque)
        __print_info(f"Sessions trouvées dans le {disque} : {''.join(liste_session)}")

        for session in liste_session:
            init_logger(f"{disque}/{session}_")
            run_session_with_args(f"{disque} {session}")
    pass


def detect_vv_and_session(folder_name: str) -> tuple[str, str]:
    """
    Extrait <VV> et vérifie le numéro de session.
    Exemple: 'APO01U1123456.SAG' -> VV='U1', session='123456'
    """
    base = os.path.splitext(os.path.basename(folder_name))[0]  # "APO01U1123456"
    # if not base.startswith("APO01"):
    #     raise ValueError(f"Nom de dossier inattendu: {folder_name}")

    suffix = base[len("APO01"):]  # "U1123456"
    vv, sess = suffix[:2], suffix[2:]
    return vv, sess


def list_sessions_from_disk(disque):
    liste_session = []
    ldsk = os.listdir(disque)
    for d in ldsk:
        el = disque + "/" + d
        if os.path.isdir(el):
            _, sess = detect_vv_and_session(d)
            liste_session.append(sess)
        pass
    liste_session = list(dict.fromkeys(liste_session))
    return liste_session


def run_session_with_args(args):
    exe_dir = os.path.dirname(sys.executable)  # répertoire du .exe actuel
    session_exe = os.path.join(exe_dir, "main_session.exe")
    if not os.path.exists(session_exe):
        raise FileNotFoundError(f"{session_exe} introuvable")

    subprocess.run([session_exe] + args, check=True)


if __name__ == "__main__":
    run_session_with_args(sys.argv[1:])

````

## src\main_gui.py

````python
import os
import subprocess
import threading
import tkinter as tk
from tkinter import filedialog, messagebox, ttk

from PIL import ImageTk, Image

from detect_text_from_frame import detect_text_from_frame, process_tags_on_image_and_update_me0
from logs import init_logger, get_logfilename


def traite_image(ficimg):
    tag = detect_text_from_frame(ficimg)
    return tag


# def process_on_images_from_directory(directory_path, type_image, fichier_de_sortie_SAG, fichier_de_sortie_SES,
#                                      pas_acq, cb_avancement, max_workers, fic_locapo):
#     start_time = time.time()
#     result = process_on_images_from_directory_parallel(directory_path=directory_path, type_image=type_image,
#                                                        cb_avancement=cb_avancement, max_workers=max_workers)
#     end_time = time.time()
#     elapsed = end_time - start_time
#     log: Logger = logging.getLogger()
#     log.info(f"\nTemps total de traitement des images : {elapsed:.2f} secondes pour {len(result)} fichiers.\n")
#     process_update_me0(list_fic_images=result, pas_acq=pas_acq, fichier_de_sortie_SAG=fichier_de_sortie_SAG,
#                        fichier_de_sortie_SES=fichier_de_sortie_SES, fichier_locapo=fic_locapo)
#     process_rename_images_from_directory(directory_path, type_image)
#     end_time = time.time()
#     elapsed = end_time - start_time
#     log.info(f"\nTemps total du traitement : {elapsed:.2f} secondes pour {len(result)} fichiers.\n")


def donne_num_session(dossier, sep):
    toks = dossier.split(sep="APO01")
    session = toks[1].split(sep=sep)[0]
    return session


class MainApp:
    MAIN_WIDTH = 1280
    MAIN_HEIGH = 800

    def __init__(self):
        # Variables de l'application
        self.dossier_ouvert = False
        self.nom_dossier_images = ""
        self.nom_fichier_me0_SES = ""
        self.nom_fichier_me0_SAG = ""
        self.nom_fichier_me0_LOCAPO = ""
        self.nom_fic_log = ""
        self.session = ""
        self.type_image = "JPG"
        self.log_actif = False
        self.titre = "Corrige ME0 IAE02 v2.22"

        # Références Tkinter
        self.root = tk.Tk()
        self.root.title(self.titre)
        self.root.geometry(f"{MainApp.MAIN_WIDTH}x{MainApp.MAIN_HEIGH}")

        # infos dossier
        self.txt_nom_dossier = tk.Entry(self.root, width=80)
        self.txt_nom_dossier.pack(padx=5, pady=5)

        self.txt_type_image = tk.Entry(self.root, width=80)
        self.txt_type_image.pack(padx=5, pady=5)

        self.txt_fic_me0_SAG = tk.Entry(self.root, width=80)
        self.txt_fic_me0_SAG.pack(padx=5, pady=5)

        self.txt_fic_me0_SES = tk.Entry(self.root, width=80)
        self.txt_fic_me0_SES.pack(padx=5, pady=5)

        self.txt_fic_me0_locAPO_SES = tk.Entry(self.root, width=80)
        self.txt_fic_me0_locAPO_SES.pack(padx=5, pady=5)

        # Frame pour les deux txt
        frame_me0 = ttk.Frame(self.root)
        frame_me0.pack(padx=5, pady=5, fill=tk.X, expand=False)

        # Texte ME0 SES à gauche
        self.txt_me0_SES = tk.Text(frame_me0, height=5, width=40)
        self.txt_me0_SES.pack(side=tk.LEFT, fill=tk.X, expand=True, padx=2)

        # Texte ME0 SAG à droite
        self.txt_me0_SAG = tk.Text(frame_me0, height=5, width=40)
        self.txt_me0_SAG.pack(side=tk.LEFT, fill=tk.X, expand=True, padx=2)

        # Champs listbox
        self.listbox_fichiers: tk.Listbox = None
        self.image_selectionnee: tk.Label = None
        self.txt_tags_image: tk.Text = None
        self.cbx_tags: ttk.Checkbutton = None
        self.cbx_tags_state = tk.IntVar(value=0)

        # traitement
        self.jge_traitement = ttk.Progressbar(self.root, orient="horizontal", mode="determinate", maximum=100)
        self.jge_traitement.pack(padx=5, pady=5, fill=tk.X, expand=False)

        # Création des menus
        self.creer_menu()
        self.creer_listbox_avec_scrollbar()

    # ---------- Menus ----------
    def creer_menu(self):
        menu_bar = tk.Menu(self.root)

        # Menu Fichier
        menu_fichier = tk.Menu(menu_bar, tearoff=0)
        menu_fichier.add_command(label="Ouvrir dossier complet", command=self.on_ouvrir_dossier_complet)
        menu_fichier.add_separator()
        menu_fichier.add_command(label="Ouvrir dossier image", command=self.on_ouvrir_dossier_image)
        menu_fichier.add_command(label="Ouvrir dossier SAG", command=self.on_ouvrir_dossier_sag)
        menu_fichier.add_command(label="Ouvrir dossier SES", command=self.on_ouvrir_dossier_ses)
        menu_fichier.add_separator()
        menu_fichier.add_command(label="Fermer dossier", command=self.on_fermer_dossier)
        menu_fichier.add_separator()
        menu_fichier.add_command(label="Quitter", command=self.root.quit)
        menu_bar.add_cascade(label="Fichier", menu=menu_fichier)

        # Menu Edition
        menu_edition = tk.Menu(menu_bar, tearoff=0)
        menu_edition.add_command(label="Ouvrir fichier ME0", command=lambda: self.ouvrir_fichier("ME0"))
        menu_edition.add_command(label="Ouvrir fichier REN", command=lambda: self.ouvrir_fichier("REN"))
        menu_edition.add_command(label="Ouvrir fichier LOG", command=lambda: self.ouvrir_fichier("LOG"))
        menu_bar.add_cascade(label="Edition", menu=menu_edition)

        # Menu Outils
        menu_outils = tk.Menu(menu_bar, tearoff=0)
        menu_type_image = tk.Menu(menu_outils, tearoff=0)
        menu_type_image.add_command(label="JPG", command=lambda: self.set_type_image("JPG"))
        menu_type_image.add_command(label="BMP", command=lambda: self.set_type_image("BMP"))
        menu_outils.add_cascade(label="Type image", menu=menu_type_image)
        menu_outils.add_checkbutton(label="Activer log", command=self.on_toggle_log)
        menu_outils.add_command(label="Traite le dossier", command=self.on_traite_dossier)
        menu_bar.add_cascade(label="Outils", menu=menu_outils)

        # Menu Aide
        menu_aide = tk.Menu(menu_bar, tearoff=0)
        menu_aide.add_command(label="À propos", command=self.a_propos)
        menu_bar.add_cascade(label="Aide", menu=menu_aide)

        self.root.config(menu=menu_bar)

    def creer_listbox_avec_scrollbar(self):
        # Cadre principal
        frame_main = ttk.Frame(self.root, padding=10)
        frame_main.pack(fill=tk.BOTH, expand=True)

        # Cadre pour Listbox + Scrollbars + Image
        frame_list = ttk.Frame(frame_main)
        frame_list.pack(fill=tk.BOTH, expand=True)

        # Scrollbars
        scrollbar_y = tk.Scrollbar(frame_list, orient=tk.VERTICAL)
        scrollbar_x = tk.Scrollbar(frame_main, orient=tk.HORIZONTAL)

        # Liste des fichiers
        self.listbox_fichiers = tk.Listbox(
            frame_list,
            yscrollcommand=scrollbar_y.set,
            xscrollcommand=scrollbar_x.set
        )
        self.listbox_fichiers.bind('<<ListboxSelect>>', self.on_selection)

        # Cadre pour image et texte
        frame_image = ttk.Frame(frame_list)

        # Label image
        self.image_selectionnee = tk.Label(frame_image, background="grey")
        self.image_selectionnee.pack(side=tk.TOP, fill=tk.BOTH, expand=True, pady=5)

        # Zone texte
        self.txt_tags_image = tk.Text(frame_image, height=2)
        self.txt_tags_image.pack(side=tk.TOP, fill=tk.X, expand=False, pady=5)

        # check boutton
        self.cbx_tags = tk.Checkbutton(frame_image, variable=self.cbx_tags_state)
        self.cbx_tags.pack(side=tk.TOP)
        self.cbx_tags.config(text="Affiche tags")

        # Placement avec grid pour l'alignement horizontal
        self.listbox_fichiers.grid(row=0, column=0, sticky="nsew")
        scrollbar_y.grid(row=0, column=1, sticky="ns")
        frame_image.grid(row=0, column=2, sticky="nsew")

        # Configuration du redimensionnement
        frame_list.grid_columnconfigure(0, weight=1)  # listbox
        frame_list.grid_columnconfigure(2, weight=1)  # frame_image
        frame_list.grid_rowconfigure(0, weight=1)

        # Scrollbar horizontale en bas sur toute la largeur
        scrollbar_x.pack(side=tk.BOTTOM, fill=tk.X)

        # Lier les scrollbars
        scrollbar_y.config(command=self.listbox_fichiers.yview)
        scrollbar_x.config(command=self.listbox_fichiers.xview)

    def on_selection(self, event):
        # Récupérer la widget source de l'événement
        listbox = event.widget
        # Obtenir l'index de la sélection courante
        selection = listbox.curselection()
        if selection:
            index = selection[0]
            valeur = listbox.get(index)
            ficimg: str = os.path.join(self.nom_dossier_images, valeur)
            max_width = self.root.winfo_width() / 2
            max_height = self.root.winfo_height() / 2
            self.affiche_image(ficimg, max_width, max_height)

    def affiche_image(self, ficimg: str, max_width, max_height):
        img = Image.open(ficimg)
        img.thumbnail((max_width, max_height))
        self.image_selectionnee.image = ImageTk.PhotoImage(img)
        self.image_selectionnee.config(image=self.image_selectionnee.image)
        self.txt_tags_image.delete("1.0", tk.END)
        if self.cbx_tags_state.get() == 1:
            tag = traite_image(ficimg)
            self.txt_tags_image.insert(tk.END, tag)

    def on_ouvrir_dossier_complet(self):
        dossier = filedialog.askdirectory()
        if dossier:
            self.dossier_ouvert = True
            fichiers = [f for f in os.listdir(dossier)]
            for f in fichiers:
                if f.lower().endswith(".ses"):
                    self.session = donne_num_session(f, ".")
                    dossier_ses = dossier + "/APO01" + self.session + ".SES"
                    dossier_sag = dossier + "/APO01" + self.session + ".SAG"
                    self.nom_fichier_me0_SAG = dossier_sag + "/APOIAE02" + self.session + ".ME0"
                    self.nom_fichier_me0_SES = dossier_ses + "/APOIAE02" + self.session + ".ME0"
                    self.nom_fichier_me0_LOCAPO = dossier_ses + "/APOLOC03" + self.session + ".ME0"
                    dossier_iae = dossier + "/IAE02" + self.session
                    if not os.path.exists(dossier_iae):
                        self.nom_dossier_images = dossier + "/APOIAE02" + self.session + ".ME0_"
                    else:
                        self.nom_dossier_images = dossier_iae + "/APOIAE02" + self.session + ".ME0_"
                    self.afficher_infos_dossier()
                    self.affiche_infos_ses()
                    self.affiche_infos_sag()

    def on_ouvrir_dossier_image(self):
        dossier = filedialog.askdirectory()
        if dossier:
            self.dossier_ouvert = True
            self.nom_dossier_images = dossier
            self.afficher_infos_dossier()

    def on_ouvrir_dossier_ses(self):
        dossier = filedialog.askdirectory()
        if dossier:
            self.session = donne_num_session(dossier, ".")
            self.nom_fichier_me0_SES = dossier + "/APOIAE02" + self.session + ".ME0"
            self.nom_fichier_me0_LOCAPO = dossier + "/APOLOC03" + self.session + ".ME0"
            self.affiche_infos_ses()

    def affiche_infos_ses(self):
        # Affichage du nom du dossier
        self.txt_fic_me0_SES.delete(0, tk.END)
        self.txt_fic_me0_SES.insert(0, self.nom_fichier_me0_SES)
        # Affichage du nom du dossier
        self.txt_fic_me0_locAPO_SES.delete(0, tk.END)
        self.txt_fic_me0_locAPO_SES.insert(0, self.nom_fichier_me0_LOCAPO)

    def on_ouvrir_dossier_sag(self):
        dossier = filedialog.askdirectory()
        if dossier:
            self.session = donne_num_session(dossier, ".")
            self.nom_fichier_me0_SAG = dossier + "/APOIAE02" + self.session + ".ME0"
            self.affiche_infos_sag()

    def affiche_infos_sag(self):
        # Affichage du nom du dossier
        self.txt_fic_me0_SAG.delete(0, tk.END)
        self.txt_fic_me0_SAG.insert(0, self.nom_fichier_me0_SAG)

    def afficher_infos_dossier(self):
        # Affichage du nom du dossier
        self.txt_nom_dossier.delete(0, tk.END)
        self.txt_nom_dossier.insert(0, self.nom_dossier_images)

        # Affichage du type image
        self.txt_type_image.delete(0, tk.END)
        self.txt_type_image.insert(0, f"Type image : {self.type_image}")

        # Affichage de la liste des fichiers
        self.listbox_fichiers.delete(0, tk.END)
        fichiers = [f for f in os.listdir(self.nom_dossier_images) if f.lower().endswith(self.type_image.lower())]
        for f in fichiers:
            self.listbox_fichiers.insert(tk.END, f)

        # jauge de progression
        self.jge_traitement.config(maximum=len(fichiers))

    def on_fermer_dossier(self):
        self.dossier_ouvert = False
        self.nom_dossier_images = ""
        self.txt_nom_dossier.delete(0, tk.END)
        self.txt_type_image.delete(0, tk.END)
        self.listbox_fichiers.delete(0, tk.END)
        self.image_selectionnee.config(image="", text="")
        self.image_selectionnee.image = None
        self.txt_tags_image.delete("1.0", tk.END)
        self.reset_traitement()

    def reset_traitement(self):
        self.jge_traitement["value"] = 0
        self.txt_me0_SES.delete("1.0", tk.END)
        self.txt_me0_SAG.delete("1.0", tk.END)

    def ouvrir_fichier(self, type_fichier: str):
        if not self.dossier_ouvert:
            messagebox.showwarning("Attention", "Aucun dossier ouvert")
            return

        if type_fichier.lower() == 'ME0'.lower():
            with open(self.nom_fichier_me0_SES, "r", encoding=None) as f:
                contenu = f.read()  # lit tout le fichier dans une seule chaîne
            self.txt_me0_SES.delete("1.0", tk.END)
            self.txt_me0_SES.insert(tk.END, contenu)

            with open(self.nom_fichier_me0_SAG, "r", encoding=None) as f:
                contenu = f.read()  # lit tout le fichier dans une seule chaîne
            self.txt_me0_SAG.delete("1.0", tk.END)
            self.txt_me0_SAG.insert(tk.END, contenu)

        elif type_fichier.lower() == "LOG".lower():
            if not os.path.exists(self.nom_fic_log):
                messagebox.showerror("Erreur", f"Aucun fichier *.{type_fichier} trouvé")
                return
            if os.name == "nt":  # Windows
                subprocess.run(["notepad.exe", self.nom_fic_log])
            else:
                subprocess.run(["xdg-open", self.nom_fic_log])

        elif type_fichier.lower() == "REN".lower():
            fic_ren = self.nom_fichier_me0_SAG + ".ren"
            if not os.path.exists(fic_ren):
                messagebox.showerror("Erreur", f"Aucun fichier *.{type_fichier} trouvé")
                return
            if os.name == "nt":  # Windows
                subprocess.run(["notepad.exe", fic_ren])
            else:
                subprocess.run(["xdg-open", fic_ren])

    def set_type_image(self, type_img):
        self.type_image = type_img
        if self.dossier_ouvert:
            self.afficher_infos_dossier()

    def on_toggle_log(self):
        self.log_actif = not self.log_actif
        print("Log actif :", self.log_actif)

    def on_traite_dossier(self):
        if not os.path.exists(self.nom_fichier_me0_SAG + ".old"):
            os.rename(self.nom_fichier_me0_SAG, self.nom_fichier_me0_SAG + ".old")
        if not os.path.exists(self.nom_fichier_me0_SES + ".old"):
            os.rename(self.nom_fichier_me0_SES, self.nom_fichier_me0_SES + ".old")
        nom_me0_sag = self.nom_fichier_me0_SAG
        nom_me0_ses = self.nom_fichier_me0_SES

        self.reset_traitement()

        init_logger(nom_me0_sag)
        self.nom_fic_log = get_logfilename()

        threading.Thread(
            # , , , fichier_de_sortie_SES, , , , fic_locapo
            target=process_tags_on_image_and_update_me0,
            kwargs={
                "directory_path": self.nom_dossier_images,
                "type_image": self.type_image.lower(),
                "pas_acq": 10,
                "fichier_de_sortie_SAG": nom_me0_sag,
                "fichier_de_sortie_SES": nom_me0_ses,
                "fic_locapo": self.nom_fichier_me0_LOCAPO,
                "cb_avancement": self.on_avancement,
                "max_workers": os.cpu_count() * (1 + 3)
            },
            daemon=True
        ).start()

    def on_avancement(self, donnee: str):
        val_incr = int(donnee.split('\t')[1])
        self.root.after(0, self.increment_jge, val_incr)

    def increment_jge(self, val):
        self.jge_traitement["value"] = val

    def a_propos(self):
        messagebox.showinfo("À propos", f"{self.titre}\n")

    # ---------- Lancer l'app ----------
    def run(self):
        self.root.mainloop()


if __name__ == "__main__":
    app = MainApp()
    app.run()

````

## src\main_session.py

````python
import os
import sys

from process_session import process_session_check
from rollback_session import rollback
from logs import init_logger
from _version import __version__

# ----------------------------
# Utilisation: corrige_session_iae02 <dossier_AAAAMMJJ_A3D_Y_T_G> <session>
# ----------------------------

if __name__ == "__main__":
    # Cas 1 : afficher la version
    if "--version" in sys.argv:
        print(__version__)
        sys.exit(0)

    # Cas 2 : traitement de la session
    if len(sys.argv) < 3:
        print(f"Usage: {sys.argv[0]} <dossier_AAAAMMJJ_A3D_Y_T_G> <session_ssssss>")
        print(f"Usage: {sys.argv[0]} rollback <dossier_AAAAMMJJ_A3D_Y_T_G> <session_ssssss>")
        base_dir = "..\\tests\\.pytest_cache\\A3D\\20250811_A3D_1_T_G\\"
        session = "250320"
        # init_logger(f"{base_dir}/{session}_")
        # process_session_check(base_dir, session)
        sys.exit(1)
    elif len(sys.argv) == 3:
        base_dir = sys.argv[1]
        session = sys.argv[2]
        init_logger(f"{base_dir}/{session}_")
        process_session_check(base_dir, session, None, os.cpu_count()*4)
        sys.exit()

    # Cas 3 : rollback
    if sys.argv[1] == "rollback":
        if len(sys.argv) != 4:
            print(f"Usage: {sys.argv[0]} rollback <dossier_AAAAMMJJ_A3D_Y_T_G> <session_ssssss>")
            sys.exit(1)
        base_dir = sys.argv[2]
        session = sys.argv[3]
        rollback(base_dir, session)

````

## src\process_disk.py

````python
#!/usr/bin/env python3
import os
import re
import sys
import shutil
from typing import Callable, TextIO
from datetime import datetime
from pathlib import Path

from process_session import process_session_check, detect_vv_and_session
from logs import __print_info, __print_erreur, init_logger, reset_logger


def process_disk(base_dir_: str, cb_avancement: Callable[[str], None] = None, max_workers: int = 4):
    __print_info("Recherche des disques A3D...")
    pattern = re.compile(r"^\d{8}_A3D_\d_[A-Z]_[A-Z]$")
    base_dir = os.path.abspath(base_dir_)
    liste_disque_aigle = [
        os.path.join(base_dir, d)
        for d in os.listdir(base_dir)
        if os.path.isdir(os.path.join(base_dir, d)) and pattern.match(d)
    ]

    if len(liste_disque_aigle) <= 0:
        if not pattern.match(Path(base_dir).name):
            __print_erreur(f"Aucun disques A3D trouvés dans le répertoire {base_dir}")
            return False
        else:
            liste_disque_aigle.append(base_dir)

    __print_info(f"Disques trouvés :{''.join(liste_disque_aigle)}")

    for disque in liste_disque_aigle:

        __print_info(f"Traitement sur le disque {disque}")

        liste_session = list_sessions_from_disk(disque)
        __print_info(f"Sessions trouvées dans le {disque} : {''.join(liste_session)}")

        for session in liste_session:
            reset_logger()
            init_logger(f"{disque}/{session}_")
            process_session_check(disque, session, cb_avancement, max_workers)
    return True


def list_sessions_from_disk(disque):
    liste_session = []
    ldsk = os.listdir(disque)
    for d in ldsk:
        el = disque + "/" + d
        if os.path.isdir(el):
            _, sess = detect_vv_and_session(d)
            liste_session.append(sess)
        pass
    liste_session = list(dict.fromkeys(liste_session))
    return liste_session


if __name__ == "__main__":
    print(f"Usage: {sys.argv[0]} <disque c:/<>>")
    process_disk("../tests/.pytest_cache/A3D/")

````

## src\process_session.py

````python
#!/usr/bin/env python3
import os
import sys
import shutil
from typing import Callable, TextIO
from datetime import datetime
from pathlib import Path

from logs import init_logger, __print_info, __print_erreur
from detect_text_from_frame import process_tags_on_image_and_update_me0
from revise_duplicated_lines_me0 import revise_duplicated_lines_me0


def __backup_file(file_path: str):
    """
    Crée une copie .old d'un fichier ME0 si elle n'existe pas déjà.
    """
    if not os.path.exists(file_path):
        __print_erreur(f"Fichier introuvable : {file_path}")
        return

    backup_path = file_path + ".old"
    if not os.path.exists(backup_path):
        shutil.copy2(file_path, backup_path)
        __print_info(f"Backup créé : {backup_path}")
    else:
        __print_info(f"Backup déjà existant : {backup_path}")
        os.rename(file_path, f"{file_path}_{datetime.now().strftime('%Y%m%d_%H%M%S')}.bak")
        shutil.copy2(file_path + ".old", file_path)


def detect_vv_and_session(folder_name: str) -> tuple[str, str]:
    """
    Extrait <VV> et vérifie le numéro de session.
    Exemple: 'APO01U1123456.SAG' -> VV='U1', session='123456'
    """
    base = os.path.splitext(os.path.basename(folder_name))[0]  # "APO01U1123456"
    # if not base.startswith("APO01"):
    #     raise ValueError(f"Nom de dossier inattendu: {folder_name}")

    suffix = base[len("APO01"):]  # "U1123456"
    vv, sess = suffix[:2], suffix[2:]
    return vv, sess


def __detect_vv_and_session_check(folder_name: str, expected_session: str) -> tuple[str, str]:
    """
    Extrait <VV> et vérifie le numéro de session.
    Exemple: 'APO01U1123456.SAG' -> VV='U1', session='123456'
    """
    sess: str
    vv: str
    vv, sess = detect_vv_and_session(folder_name)

    if sess != expected_session:
        raise ValueError(f"Incohérence: session attendue {expected_session}, trouvée {sess}")

    return vv, sess


def process_session(base_dir: str, session: str, cb_avancement: Callable[[str], None] = None,
                    max_workers: int = 4) -> bool:
    """
    pour une session donnée
    """
    image_me0_dir, loc_me0, sag_me0, ses_me0, vv = get_me0_and_imagesfolder(base_dir, session)
    if not image_me0_dir:
        return False

    # Backup des fichiers
    __backup_file(sag_me0)
    __backup_file(ses_me0)
    nb_ligne_me0: int = 0
    nb_images_ren: int = 0

    # Exécution selon VV
    if vv == "U1":
        __print_info("traitement sur session Aigle1")
        nb_ligne_me0, nb_images_ren = process_tags_on_image_and_update_me0(image_me0_dir, ".JPG", 10, sag_me0, ses_me0,
                                                                           loc_me0, cb_avancement, max_workers
                                                                           )

    elif vv == "U2":
        __print_info("traitement sur session Aigle2")
        __print_info("traitement sur session SAG")
        nb_ligne_me0 = revise_duplicated_lines_me0(sag_me0)
        __print_info("traitement sur session SES")
        nb_ligne_me0 = revise_duplicated_lines_me0(ses_me0)

    else:
        __print_erreur(f"Type VV non reconnu : {vv}")
        return False

    # Écriture du fichier log
    log_sessions = get_log_current_session(base_dir)
    with open(log_sessions, "a", encoding="utf-8") as f:
        f.write(f"\n{datetime.now().strftime('%Y%m%d_%H%M%S')}\t{session}\t{vv}\t{nb_ligne_me0}\t{nb_images_ren}")

    __print_info(f"--- Fin traitement session {session} ---\n")
    return True


def get_me0_and_imagesfolder(base_dir, session):
    __print_info(f"--- Début traitement session {session} dans {base_dir} ---")

    # Recherche SAG / SES
    images_dir: str
    sag_dir: str
    ses_dir: str
    images_dir, sag_dir, ses_dir = __list_session_folders(base_dir, session)
    if not sag_dir or not ses_dir or not images_dir:
        __print_erreur("Impossible de trouver SAG / SES / IAE02 pour la session.")
        return None, None, None, None, None

    __print_info(f"Répertoires trouvés : SAG={sag_dir}, SES={ses_dir}, IAE02={images_dir}")

    image_me0_dir: str
    loc_me0: str
    sag_me0: str
    ses_me0: str
    vv: str
    image_me0_dir, loc_me0, sag_me0, ses_me0, vv = __me0_and_imagesfolder(images_dir, sag_dir, ses_dir, session)

    return image_me0_dir, loc_me0, sag_me0, ses_me0, vv


def __me0_and_imagesfolder(images_dir: str, sag_dir: str, ses_dir: str, session: str) \
        -> tuple[str, str, str, str]:
    # Détection du code VV
    vv, session_check = __detect_vv_and_session_check(os.path.basename(sag_dir), session)
    __print_info(f"Session {session} détectée avec VV={vv}")
    # Fichiers ME0
    sag_me0: str = os.path.join(sag_dir, f"APOIAE02{vv}{session}.ME0")
    ses_me0: str = os.path.join(ses_dir, f"APOIAE02{vv}{session}.ME0")
    loc_me0: str = os.path.join(ses_dir, f"APOLOC03{vv}{session}.ME0")
    image_me0_dir: str = os.path.join(images_dir, f"APOIAE02{vv}{session}.ME0_")
    return image_me0_dir, loc_me0, sag_me0, ses_me0, vv


def __list_session_folders(base_dir: str, session: str) -> tuple[str, str, str]:
    sag_dir: str = None
    ses_dir: str = None
    images_dir: str = None
    for item in os.listdir(base_dir):
        if item.startswith("APO01") and item.endswith(f"{session}.SAG"):
            sag_dir = os.path.join(base_dir, item)
        elif item.startswith("APO01") and item.endswith(f"{session}.SES"):
            ses_dir = os.path.join(base_dir, item)
        elif item.startswith("IAE02") and item.endswith(session):
            images_dir = os.path.join(base_dir, item)
    return images_dir, sag_dir, ses_dir


def process_session_check(base_dir: str, session: str, cb_avancement: Callable[[str], None] = None,
                          max_workers: int = 4) -> bool:
    log_sessions = get_log_current_session(base_dir)
    if os.path.exists(log_sessions):
        with open(log_sessions, "r", encoding="utf-8") as f:
            lines = f.read().splitlines()
            for line in lines:
                if line != "" and line.split("\t")[1] == session:
                    __print_info("Session déjà traitée")
                    return False
    else:
        with open(log_sessions, "w", encoding="utf-8") as f:
            f.writelines("Date\tSession\tID\tNombre de ligne ME0\tNombre d'images renommées\n")
    return process_session(base_dir, session, cb_avancement, max_workers)


def get_log_current_session(base_dir: str) -> str:
    log_sessions = os.path.join(os.path.abspath(base_dir), Path(os.path.abspath(base_dir)).name + ".txt")
    return log_sessions


if __name__ == "__main__":
    print(f"Usage: {sys.argv[0]} <dossier_AAAAMMJJ_A3D_Y_T_G> <session>")
    process_session_check("../tests/.pytest_cache/A3D/20250811_A3D_1_T_G", "250320")
    process_session_check("../tests/.pytest_cache/A3D/20250811_A3D_2_T_G", "250310")

````

## src\revise_duplicated_lines_me0.py

````python
import os
import sys
import shutil
import glob
from datetime import datetime

from logs import init_logger, __print_debug, __print_info, __print_erreur


def revise_duplicated_lines_me0(fichier: str) -> int:
    __print_info(f"Corrige le fichier {fichier}...")

    # Lecture ANSI (cp1252 pour Windows)
    __print_info('lecture du fichier')
    with open(fichier, "r", errors="replace") as f:
        contenu = f.read()

    lignes = contenu.splitlines()
    if not lignes:
        __print_erreur(f"Fichier vide : {fichier}")
        return 0
    __print_info(f'Nombre de ligne du fichier {len(lignes)}')

    header = lignes[0].strip()
    nouvelles_lignes = [header]

    if header.lower().startswith("dist(m)"):
        __print_info(f"fichier ME0 type SAG")
        process_me0_sag(lignes, nouvelles_lignes)

    elif header.lower().startswith("abd"):
        __print_info(f"fichier ME0 type SES")
        process_me0_ses(lignes, nouvelles_lignes)

    else:
        __print_erreur(f"En-tête inconnu dans {fichier}")
        return 0

    # Sauvegarde .bak et réécriture en ANSI
    if not os.path.exists(fichier + ".old"):
        __print_info("Sauvegarde .bak")
        shutil.copy2(fichier, fichier + ".old")
    else:
        __print_info("Fichier déjà sauvegardé")

    __print_info(f"Réécriture dans fichier ME0 {fichier}")
    # with open(fichier, "w", encoding="cp1252", errors="replace") as f:
    with open(fichier, "w", errors="replace") as f:
        f.write("\n".join(nouvelles_lignes))

    __print_info(f"Corrigé : {fichier}")
    return len(nouvelles_lignes)


def process_me0_ses(lignes, nouvelles_lignes):
    i = 1
    while i < len(lignes) - 1:
        ligne1 = lignes[i].strip()
        ligne2 = lignes[i + 1].strip()

        champs1 = ligne1.split("\t")
        champs2 = ligne2.split("\t")

        if len(champs1) == 6 and len(champs2) == 5:
            num_image = champs1[4]
            image_path = champs1[5].split(".BMP")[0] + f".{num_image}.JPG"
            temps = champs2[4]
            nouvelle_ligne = (
                f"{champs1[0]}\t{champs1[1]}\t{champs1[2]}\t{champs1[3]}\t"
                f"{num_image}\t{image_path}\t{temps}"
            )
            nouvelles_lignes.append(nouvelle_ligne)
            i += 2
        else:
            nouvelles_lignes.append(ligne1)
            i += 1
    # Dernière ligne si non traitée
    if i < len(lignes):
        nouvelles_lignes.append(lignes[i])


def process_me0_sag(lignes, nouvelles_lignes):
    i = 1
    while i < len(lignes):
        ligne = lignes[i].strip()
        champs = ligne.split("\t")

        if len(champs) == 4:
            nouvelles_lignes.append(ligne)
            i += 1

        elif len(champs) == 3 and (i + 1) < len(lignes):
            ligne_suivante = lignes[i + 1].strip()
            ligne_suivante = ligne_suivante.split("\t")[1]
            num_image = champs[1]
            champ_image_corrige = (
                    champs[2].split(".BMP")[0] + f".{num_image}.JPG"
            )
            ligne_corrigee = f"{champs[0]}\t{num_image}\t{champ_image_corrige}\t{ligne_suivante}"
            nouvelles_lignes.append(ligne_corrigee)
            i += 2
        else:
            nouvelles_lignes.append(ligne)
            i += 1


if __name__ == "__main__":
    rep = sys.argv[1] if len(sys.argv) > 1 else "."
    ext = sys.argv[2] if len(sys.argv) > 2 else "ME0"


    def main(repertoire=".", extension="ME0"):
        pattern = os.path.join(repertoire, f"*.{extension}")
        for fichier in glob.glob(pattern):
            revise_duplicated_lines_me0(fichier)


    # main(rep, ext)
    from detect_text_from_frame import init_logger
    init_logger("d:/tmp/iea02/log")
    revise_duplicated_lines_me0(f"D:/tmp/iea02/APO01U1250320.SES/APOIAE02U1250320.ME0")

````

## src\rollback_session.py

````python
import os
import sys
from datetime import datetime
from pathlib import Path
from logs import __print_info, __print_erreur
from process_session import __list_session_folders, __me0_and_imagesfolder


def rename_back_images(fic_renommage="./renommage.txt"):
    __print_info(f"ouverture fichier {fic_renommage}")
    with open(fic_renommage, "r", encoding="utf-8") as f:
        lines = f.read().splitlines()
        __print_info(f"nombre de lignes dans le fichier {len(lines)}")
        for line in lines:
            line = line.strip()
            if not line:
                continue

            ancien, nouveau = line.split("\t")

            # On enlève le '_' final si présent dans le "nouveau"
            nouveau = nouveau.rstrip("_")

            # Extraire juste les noms des fichiers
            ancien_nom = os.path.basename(ancien) + "_"
            nouveau_nom = os.path.basename(nouveau)

            # Extraire le dossier
            dossier = os.path.dirname(nouveau)

            src = os.path.join(dossier, nouveau_nom)
            dst = os.path.join(dossier, ancien_nom)

            if not os.path.exists(dst):
                if os.path.exists(src):
                    __print_info(f"Renomme {src} -> {dst}")
                    os.rename(src, dst)
                else:
                    __print_erreur(f"!!! Fichier introuvable : {src}")
            else:
                __print_erreur(f"!!! Fichier déjà : {dst}")

    for f in os.listdir(dossier):
        if f.lower().endswith(".jpg_"):
            src = os.path.join(dossier, f)
            dst = src[:-1]
            if not os.path.exists(dst):
                __print_info(f"Renomme le fichier {src}->{dst}")
                os.rename(src, dst)
    __print_info("renommage fini")


def rollback(disk_: str, session: str):
    disk = os.path.abspath(disk_)
    log_sessions = os.path.join(disk, Path(disk).name + ".txt")
    fic_modif = False
    if os.path.exists(log_sessions):
        __print_info(f"Recherche la session {session} dans le log {log_sessions}")
        with open(log_sessions, "r+") as f:
            lines = f.read().splitlines()
            for line in lines:
                if line != "" and line.split("\t")[1] == session:
                    __print_info(f"Session {session} trouvée dans le log {log_sessions}")
                    __rollback_run(disk, session)
                    __print_info(f"Supprime la session {session} dans le log {log_sessions}")
                    lines.remove(line)
                    fic_modif = True
                    break
        if fic_modif:
            with open(log_sessions, "w+") as f:
                f.writelines(lines)
        else:
            __print_info(f"Pas de session {session} dans le log {log_sessions}")
            return False

    return True


def __rollback_run(disk: str, session: str):
    __print_info(f"Rollback sur le disque {disk} de la session {session}")
    images_dir, sag_dir, ses_dir = __list_session_folders(disk, session)
    if not sag_dir or not ses_dir or not images_dir:
        __print_erreur("Impossible de trouver SAG / SES / IAE02 pour la session.")
        return False

    image_me0_dir, loc_me0, sag_me0, ses_me0, vv = __me0_and_imagesfolder(images_dir, sag_dir, ses_dir, session)
    if os.path.exists(f"{sag_me0}.old"):
        __print_info(f"Restaure le fichier {sag_me0}")
        os.remove(sag_me0)
        os.rename(f"{sag_me0}.old", sag_me0)

    if os.path.exists(f"{ses_me0}.old"):
        __print_info(f"Restaure le fichier {ses_me0}")
        os.remove(ses_me0)
        os.rename(f"{ses_me0}.old", ses_me0)

    if os.path.exists(f"{image_me0_dir}/renommage.txt"):
        __print_info(f"Restaure le nom des images du dossier {image_me0_dir}")
        rename_back_images(f"{image_me0_dir}/renommage.txt")
        os.rename(f"{image_me0_dir}/renommage.txt",
                  f"{image_me0_dir}/renommage_{datetime.now().strftime('%Y%m%d_%H%M%S')}.old")
        os.rename(f"{image_me0_dir}/tags.txt",
                  f"{image_me0_dir}/tags_{datetime.now().strftime('%Y%m%d_%H%M%S')}.old")
    return True


if __name__ == "__main__":
    rep = sys.argv[1] if len(sys.argv) > 1 else "D:\\tmp\\APOIAE02U1250320.ME0_/renommage.txt"
    __print_info(f"Traitement du dossier {rep}")
    rollback("../tests/.pytest_cache/A3D/20250811_A3D_1_T_G", "250320")
    rollback("../tests/.pytest_cache/A3D/20250811_A3D_2_T_G", "250310")

````

# setup

## setup\build.spec

````
# -*- mode: python ; coding: utf-8 -*-

import os
import sys
import subprocess
from pathlib import Path

## Récupère le niveau (maj / min / patch) passé en argument à pyinstaller
#level = "patch"
#for arg in sys.argv:
#    if arg in ["maj", "min", "patch"]:
#        level = arg
#
## Appelle version.py AVANT build
#subprocess.run([sys.executable, "setup/version.py", level], check=True)

# Lis la version mise à jour
#root = Path(__file__).resolve().parent.parent
VERSION = Path("VERSION").read_text().strip()
print(VERSION)

main_script_1 = '../src/main_disk.py'
tesseract_folder = r"C:\Program Files\Tesseract-OCR"

datas = [
    (tesseract_folder, 'Tesseract-OCR'),
]

block_cipher = None

a = Analysis(
    [main_script_1],
    pathex=["."],
    binaries=[],
    datas=datas,
    hiddenimports=[],
    hookspath=[],
    runtime_hooks=[],
    excludes=[],
    win_no_prefer_redirects=False,
    win_private_assemblies=False,
    cipher=block_cipher,
    noarchive=False,
)

pyz = PYZ(a.pure, a.zipped_data, cipher=block_cipher)

# --- exe main_disk ---
exe = EXE(
    pyz,
    a.scripts,
    a.binaries,
    a.zipfiles,
    a.datas,
    [],
    name=f'corrige_disque_iae02_v{VERSION}',
    debug=False,
    bootloader_ignore_signals=False,
    strip=False,
    upx=True,
    runtime_tmpdir=None,
    console=True
)

# --- exe secondaire ---
main_script_2 = '../src/main_session.py'

a2 = Analysis(
    [main_script_2],
    pathex=["."],
    binaries=[],
    datas=datas,
    hiddenimports=[],
    hookspath=[],
    runtime_hooks=[],
    excludes=[],
    win_no_prefer_redirects=False,
    win_private_assemblies=False,
    cipher=block_cipher,
    noarchive=False,
)

pyz2 = PYZ(a2.pure, a2.zipped_data, cipher=block_cipher)

exe2 = EXE(
    pyz2,
    a2.scripts,
    a2.binaries,
    a2.zipfiles,
    a.datas,
    [],
    name=f'corrige_session_iae02_v{VERSION}',
    debug=False,
    bootloader_ignore_signals=False,
    strip=False,
    upx=True,
    runtime_tmpdir=None,
    console=True
)

````

## setup\corrige_disque_iae02.spec

````
# -*- mode: python ; coding: utf-8 -*-

import os
import sys
import subprocess
from pathlib import Path

# Récupère le niveau (maj / min / patch) passé en argument à pyinstaller
level = "patch"
for arg in sys.argv:
    if arg in ["maj", "min", "patch"]:
        level = arg

# Appelle version.py AVANT build
subprocess.run([sys.executable, "setup/version.py", level], check=True)

# Lis la version mise à jour
VERSION = Path("VERSION").read_text().strip()
print(VERSION)

main_script_1 = '../src/main_disk.py'
tesseract_folder = r"C:\Program Files\Tesseract-OCR"

datas = [
    (tesseract_folder, 'Tesseract-OCR'),
]

block_cipher = None

a = Analysis(
    [main_script_1],
    pathex=["."],
    binaries=[],
    datas=datas,
    hiddenimports=[],
    hookspath=[],
    runtime_hooks=[],
    excludes=[],
    win_no_prefer_redirects=False,
    win_private_assemblies=False,
    cipher=block_cipher,
    noarchive=False,
)

pyz = PYZ(a.pure, a.zipped_data, cipher=block_cipher)

# --- exe main_disk ---
exe = EXE(
    pyz,
    a.scripts,
    a.binaries,
    a.zipfiles,
    a.datas,
    [],
    name=f'corrige_disque_iae02_v{VERSION}',
    debug=False,
    bootloader_ignore_signals=False,
    strip=False,
    upx=True,
    runtime_tmpdir=None,
    console=True
)

````

## setup\corrige_me0_iae02.spec

````
# -*- mode: python ; coding: utf-8 -*-

import os

main_script = '../src/main.py'
tesseract_folder = r"C:\Program Files\Tesseract-OCR"

datas = [
    (tesseract_folder, 'Tesseract-OCR'),
]

block_cipher = None

a = Analysis(
    [main_script],
    pathex=["."],
    binaries=[],
    datas=datas,
    hiddenimports=[],
    hookspath=[],
    runtime_hooks=[],
    excludes=[],
    win_no_prefer_redirects=False,
    win_private_assemblies=False,
    cipher=block_cipher,
    noarchive=False,
)

pyz = PYZ(a.pure, a.zipped_data, cipher=block_cipher)

exe = EXE(
    pyz,
    a.scripts,
    a.binaries,
    a.zipfiles,
    a.datas,
    [],
    name='corrige_me0_iae02',
    debug=False,
    bootloader_ignore_signals=False,
    strip=False,
    upx=True,
    runtime_tmpdir=None,
    console=True
)

````

## setup\corrige_session_iae02.spec

````
# -*- mode: python ; coding: utf-8 -*-

import os
import sys
import subprocess
from pathlib import Path

# Récupère le niveau (maj / min / patch) passé en argument à pyinstaller
level = "patch"
for arg in sys.argv:
    if arg in ["maj", "min", "patch"]:
        level = arg

# Appelle version.py AVANT build
subprocess.run([sys.executable, "setup/version.py", level], check=True)

# Lis la version mise à jour
VERSION = Path("VERSION").read_text().strip()
print(VERSION)

main_script = '../src/main_session.py'
tesseract_folder = r"C:\Program Files\Tesseract-OCR"

datas = [
    (tesseract_folder, 'Tesseract-OCR'),
]

block_cipher = None

a = Analysis(
    [main_script],
    pathex=["."],
    binaries=[],
    datas=datas,
    hiddenimports=[],
    hookspath=[],
    runtime_hooks=[],
    excludes=[],
    win_no_prefer_redirects=False,
    win_private_assemblies=False,
    cipher=block_cipher,
    noarchive=False,
)

pyz = PYZ(a.pure, a.zipped_data, cipher=block_cipher)

exe = EXE(
    pyz,
    a.scripts,
    a.binaries,
    a.zipfiles,
    a.datas,
    [],
    name=f'corrige_session_iae02_v{VERSION}',
    debug=False,
    bootloader_ignore_signals=False,
    strip=False,
    upx=True,
    runtime_tmpdir=None,
    console=True
)

````

## setup\gui_corrige_me0_iae02.spec

````
# -*- mode: python ; coding: utf-8 -*-

import os

main_script = '../src/main_gui.py'
tesseract_folder = r"C:\Program Files\Tesseract-OCR"

datas = [
    (tesseract_folder, 'Tesseract-OCR'),
]

block_cipher = None

a = Analysis(
    [main_script],
    pathex=["."],
    binaries=[],
    datas=datas,
    hiddenimports=[],
    hookspath=[],
    runtime_hooks=[],
    excludes=[],
    win_no_prefer_redirects=False,
    win_private_assemblies=False,
    cipher=block_cipher,
    noarchive=False,
)

pyz = PYZ(a.pure, a.zipped_data, cipher=block_cipher)

exe = EXE(
    pyz,
    a.scripts,
    a.binaries,
    a.zipfiles,
    a.datas,
    [],
    name='gui_corrige_me0_iae02',
    debug=False,
    bootloader_ignore_signals=False,
    strip=False,
    upx=True,
    runtime_tmpdir=None,
    console=True
)

````

# tests

## tests\rename_token.py

````python
#!/usr/bin/env python3
import argparse
import os
import sys


def parse_args():
    p = argparse.ArgumentParser(description="Renommer OLD -> NEW dans noms de fichiers et dossiers")
    p.add_argument("old", help="Ancien token (ex: U1250320)")
    p.add_argument("new", help="Nouveau token (ex: U1250310)")
    p.add_argument("root", nargs="?", default=".", help="Dossier racine (par défaut: .)")
    p.add_argument("--dry-run", action="store_true", help="Afficher les changements sans les appliquer")
    return p.parse_args()


def safe_rename(src, dst, dry_run):
    if os.path.exists(dst):
        print(f"SKIP (exists): {src} -> {dst}")
        return False
    if dry_run:
        print(f"RENAME: {src} -> {dst}")
        return True
    os.rename(src, dst)
    print(f"OK:     {src} -> {dst}")
    return True


def main():
    args = parse_args()
    OLD = args.old
    NEW = args.new
    ROOT = os.path.abspath(args.root)
    dry = args.dry_run

    print(f"Root: {ROOT}")
    print(f"Replace: '{OLD}' -> '{NEW}'")
    if dry: print("Mode: dry-run (aucune modification)")

    # 1) fichiers — marcher récursivement
    for dirpath, dirnames, filenames in os.walk(ROOT):
        for fname in filenames:
            if OLD in fname:
                src = os.path.join(dirpath, fname)
                newname = fname.replace(OLD, NEW)
                dst = os.path.join(dirpath, newname)
                safe_rename(src, dst, dry)

    # 2) dossiers — topdown=False pour renommer enfants avant parents
    for dirpath, dirnames, filenames in os.walk(ROOT, topdown=False):
        for dname in dirnames:
            if OLD in dname:
                src = os.path.join(dirpath, dname)
                newname = dname.replace(OLD, NEW)
                dst = os.path.join(dirpath, newname)
                safe_rename(src, dst, dry)

    print("Terminé.")


if __name__ == "__main__":
    main()

````

## tests\test_detect_text_from_frame.py

````python
import io
import os
import tempfile

import pytest
from requests import delete

from src.detect_text_from_frame import normalize_ocr_line, detect_text_from_frame, \
    process_on_images_from_directory_parallel, process_rename_images_from_directory, \
    extract_tags_from_image_and_update_me0_sag, extract_token_from_line_and_update_me0_ses


def test_normalize_ocr_line():
    textes_in = ["2025-04-01 11:27:36 0027 .363km 081km/h",
                 "2025-04-01 11:56:35 0054.442?km O089km/h",
                 "2025-04-01 11.56.35 0054 442km 089km/h", ]
    textes_out = ["2025-04-01 11:27:36 0027.363km 081km/h",
                  "2025-04-01 11:56:35 0054.442km 089km/h",
                  "2025-04-01 11:56:35 0054.442km 089km/h", ]
    for i in range(3):
        assert (textes_out[i] == normalize_ocr_line(textes_in[i]))
    assert True


def test_detect_text_from_frame():
    assert (detect_text_from_frame(
        "A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_/APOIAE02U1250320.ME0_Image.1.000001.JPG")
            == "2025-04-01 10:58:08 0000.010km 008km/h")
    assert (detect_text_from_frame(
        "A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_/APOIAE02U1250320.ME0_Image.1.000002.JPG")
            == "2025-04-01 10:58:11 0000.020km 014km/h")
    assert True


def test_process_on_images_from_directory_parallel():
    liste_images_tags = [
        "A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_Image.1.000001.JPG	2025-04-01 10:58:08 0000.010km 008km/h",
        "A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_Image.1.000002.JPG	2025-04-01 10:58:11 0000.020km 014km/h",
        "A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_Image.1.000003.JPG	2025-04-01 10:58:12 0000.030km 018km/h",
        "A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_Image.1.000004.JPG	2025-04-01 10:58:14 0000.040km 022km/h",
        "A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_Image.1.000005.JPG	2025-04-01 10:58:15 0000.050km 024km/h",
        "A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_Image.1.000006.JPG	2025-04-01 10:58:17 0000.060km 025km/h",
    ]
    result = process_on_images_from_directory_parallel(
        "A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_", ".jpg")
    for i in range(len(liste_images_tags)):
        assert (liste_images_tags[i].split("\t")[1] == result[i].split("\t")[1])
    assert True


def test_extract_tags_from_image_and_update_me0_sag():
    liste_images = [
        "APOIAE02U1250320.ME0_Image.1.000001.JPG	2025-04-01 10:58:08 0000.010km 008km/h",
        "APOIAE02U1250320.ME0_Image.1.000002.JPG	2025-04-01 10:58:11 0000.020km 014km/h",
        "APOIAE02U1250320.ME0_Image.1.000003.JPG	2025-04-01 10:58:12 0000.030km 018km/h",
        "APOIAE02U1250320.ME0_Image.1.000004.JPG	2025-04-01 10:58:14 0000.040km 022km/h",
        "APOIAE02U1250320.ME0_Image.1.000005.JPG	2025-04-01 10:58:15 0000.050km 024km/h",
        "APOIAE02U1250320.ME0_Image.1.000006.JPG	2025-04-01 10:58:17 0000.060km 025km/h",
    ]
    f_me0 = io.StringIO()
    f_renommage = io.StringIO()
    tacq: float = 0
    pas_acq_dernier: int = -1
    for i in range(len(liste_images)):
        tacq, pas_acq_dernier, line = extract_tags_from_image_and_update_me0_sag(
            f_me0, f_renommage, liste_images[i], 10, tacq, pas_acq_dernier)
        assert (not (line == ""))
    assert True


def test_extract_token_from_line_and_update_me0_ses():
    ligne_formatees = [
        "010.0\t000001\t.\\APOIAE02U1250320.ME0_Image.1.000001.JPG\t0.0",
        "020.0\t000002\t.\\APOIAE02U1250320.ME0_Image.1.000002.JPG\t0.1",
        "030.0\t000003\t.\\APOIAE02U1250320.ME0_Image.1.000003.JPG\t0.3",
        "040.0\t000004\t.\\APOIAE02U1250320.ME0_Image.1.000004.JPG\t1.1",
        "050.0\t000005\t.\\APOIAE02U1250320.ME0_Image.1.000005.JPG\t1.2",
        "060.0\t000006\t.\\APOIAE02U1250320.ME0_Image.1.000006.JPG\t2.0",
    ]
    ligne_formatees_res = [
        "10.000\t342062.473\t6693009.326\t8.700\t000001\t.\\APOIAE02U1250320.ME0_Image.1.000001.JPG\t0.0",
        "20.000\t342057.795\t6693000.552\t8.431\t000002\t.\\APOIAE02U1250320.ME0_Image.1.000002.JPG\t0.1",
        '30.000\t342053.009\t6692991.835\t8.089\t000003\t.\\APOIAE02U1250320.ME0_Image.1.000003.JPG\t0.3',
        '40.000\t342047.455\t6692983.597\t7.788\t000004\t.\\APOIAE02U1250320.ME0_Image.1.000004.JPG\t1.1',
        '50.000\t342041.493\t6692975.320\t7.580\t000005\t.\\APOIAE02U1250320.ME0_Image.1.000005.JPG\t1.2',
        '60.000\t342035.549\t6692967.218\t7.400\t000006\t.\\APOIAE02U1250320.ME0_Image.1.000006.JPG\t2.0',
    ]
    gps_data: list[dict[str, float]] = [
        {'ABD': 0.2, 'XDEB': 342070.48, 'YDEB': 6693012.55, 'ZDEB': 9.1},
        {'ABD': 0.92, 'XDEB': 342069.82, 'YDEB': 6693012.81, 'ZDEB': 9.1},
        {'ABD': 2.05, 'XDEB': 342068.77, 'YDEB': 6693012.99, 'ZDEB': 9.0},
        {'ABD': 3.47, 'XDEB': 342067.41, 'YDEB': 6693013.07, 'ZDEB': 9.0},
        {'ABD': 5.03, 'XDEB': 342065.87, 'YDEB': 6693012.72, 'ZDEB': 8.9},
        {'ABD': 6.97, 'XDEB': 342064.36, 'YDEB': 6693011.58, 'ZDEB': 8.8},
        {'ABD': 9.46, 'XDEB': 342062.74, 'YDEB': 6693009.79, 'ZDEB': 8.7},
        {'ABD': 12.72, 'XDEB': 342061.13, 'YDEB': 6693006.99, 'ZDEB': 8.7},
        {'ABD': 16.77, 'XDEB': 342059.17, 'YDEB': 6693003.33, 'ZDEB': 8.5},
        {'ABD': 21.42, 'XDEB': 342057.19, 'YDEB': 6692999.33, 'ZDEB': 8.4},
        {'ABD': 26.62, 'XDEB': 342054.78, 'YDEB': 6692994.58, 'ZDEB': 8.2},
        {'ABD': 32.69, 'XDEB': 342051.6, 'YDEB': 6692989.65, 'ZDEB': 8.0},
        {'ABD': 39.17, 'XDEB': 342047.95, 'YDEB': 6692984.3, 'ZDEB': 7.8},
        {'ABD': 45.84, 'XDEB': 342043.97, 'YDEB': 6692978.65, 'ZDEB': 7.7},
        {'ABD': 52.76, 'XDEB': 342039.85, 'YDEB': 6692973.11, 'ZDEB': 7.5},
        {'ABD': 59.84, 'XDEB': 342035.64, 'YDEB': 6692967.35, 'ZDEB': 7.4},
        {'ABD': 66.99, 'XDEB': 342031.57, 'YDEB': 6692961.47, 'ZDEB': 7.4},
        {'ABD': 74.06, 'XDEB': 342027.15, 'YDEB': 6692955.95, 'ZDEB': 7.4},
        {'ABD': 80.73, 'XDEB': 342022.75, 'YDEB': 6692950.88, 'ZDEB': 7.8},
        {'ABD': 86.82, 'XDEB': 342018.68, 'YDEB': 6692946.11, 'ZDEB': 7.9},
        {'ABD': 92.3, 'XDEB': 342015.09, 'YDEB': 6692941.99, 'ZDEB': 7.9},
    ]
    f_ses = io.StringIO()
    for ligne in ligne_formatees:
        extract_token_from_line_and_update_me0_ses(f_ses, ligne, gps_data)
    res = f_ses.getvalue().splitlines()
    for i in range(len(res)):
        assert (ligne_formatees_res[i] == res[i])
    assert True


def test_process_update_me0():
    assert True


def test_process_rename_images_from_directory():
    for i in range(10):
        open(f".pytest_cache/test{i}.jpg_", "w+")
    process_rename_images_from_directory(".pytest_cache", ".JPG")
    for f in os.listdir(".pytest_cache"):
        assert (not (os.path.isfile(f) and f.lower().endswith(".jpg_")))
    import glob
    files = glob.glob(os.path.join(".pytest_cache", "*.j*"))
    for f in files:
        os.remove(f)
    assert True


def test_process_tags_on_image_and_update_me0():
    assert True

````

## tests\test_process_disk.py

````python
import pytest
from src import process_disk
import os.path
import shutil

import pytest
from src import process_session


def test_process_disk():
    shutil.rmtree(os.path.dirname(".pytest_cache/A3D"))
    os.makedirs(os.path.dirname(".pytest_cache/A3D"), exist_ok=True)
    shutil.copytree("A3D", ".pytest_cache/A3D", dirs_exist_ok=True)
    assert (process_disk.process_disk(".pytest_cache/A3D/20250811_A3D_1_T_G/"))
    assert (os.path.exists(".pytest_cache/A3D/20250811_A3D_1_T_G/20250811_A3D_1_T_G.txt"))
    assert (os.path.exists(".pytest_cache/A3D/20250811_A3D_1_T_G/APO01U1250320.SAG/APOIAE02U1250320.ME0.old"))
    assert (os.path.exists(".pytest_cache/A3D/20250811_A3D_1_T_G/APO01U1250320.SES/APOIAE02U1250320.ME0.old"))
    assert (os.path.exists(".pytest_cache/A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_/renommage.txt"))
    assert (os.path.exists(".pytest_cache/A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_/tags.txt"))
    assert (not os.path.exists(".pytest_cache/A3D/20250811_A3D_2_T_G/20250811_A3D_2_T_G.txt"))
    assert (not os.path.exists(".pytest_cache/A3D/20250811_A3D_2_T_G/APO01U2250310.SES/APOIAE02U2250310.ME0.old"))
    assert (not os.path.exists(".pytest_cache/A3D/20250811_A3D_2_T_G/APO01U2250310.SAG/APOIAE02U2250310.ME0.old"))
    assert True


def test_list_sessions_from_disk():
    # TODO: add test for list_sessions_from_disk
    assert True

````

## tests\test_process_session.py

````python
import os.path
import shutil

import pytest
from src import process_session


def test___backup_file():
    assert True


def test_detect_vv_and_session():
    vv, ss = process_session.detect_vv_and_session("APO01U1123456")
    assert (vv == "U1")
    assert (ss == "123456")
    assert True


def test___detect_vv_and_session_check():
    assert True


def test_process_session():
    shutil.rmtree(os.path.dirname(".pytest_cache/A3D"))
    os.makedirs(os.path.dirname(".pytest_cache/A3D"), exist_ok=True)
    shutil.copytree("A3D", ".pytest_cache/A3D", dirs_exist_ok=True)
    assert(process_session.process_session(".pytest_cache/A3D/20250811_A3D_1_T_G", "250320"))
    assert(os.path.exists(".pytest_cache/A3D/20250811_A3D_1_T_G/20250811_A3D_1_T_G.txt"))
    assert(os.path.exists(".pytest_cache/A3D/20250811_A3D_1_T_G/APO01U1250320.SAG/APOIAE02U1250320.ME0.old"))
    assert(os.path.exists(".pytest_cache/A3D/20250811_A3D_1_T_G/APO01U1250320.SES/APOIAE02U1250320.ME0.old"))
    assert(os.path.exists(".pytest_cache/A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_/renommage.txt"))
    assert(os.path.exists(".pytest_cache/A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_/tags.txt"))
    assert(process_session.process_session(".pytest_cache/A3D/20250811_A3D_2_T_G/", "250310"))
    assert(os.path.exists(".pytest_cache/A3D/20250811_A3D_2_T_G/20250811_A3D_2_T_G.txt"))
    assert(os.path.exists(".pytest_cache/A3D/20250811_A3D_2_T_G/APO01U2250310.SES/APOIAE02U2250310.ME0.old"))
    assert(os.path.exists(".pytest_cache/A3D/20250811_A3D_2_T_G/APO01U2250310.SAG/APOIAE02U2250310.ME0.old"))
    assert True


def test_get_me0_and_imagesfolder():
    image_me0_dir, loc_me0, sag_me0, ses_me0, vv = process_session.get_me0_and_imagesfolder("A3D/20250811_A3D_1_T_G",
                                                                                            "250320")
    assert (image_me0_dir == "A3D/20250811_A3D_1_T_G\\IAE02U1250320\\APOIAE02U1250320.ME0_")
    assert (sag_me0 == "A3D/20250811_A3D_1_T_G\\APO01U1250320.SAG\\APOIAE02U1250320.ME0")
    assert (ses_me0 == "A3D/20250811_A3D_1_T_G\\APO01U1250320.SES\\APOIAE02U1250320.ME0")
    assert (loc_me0 == "A3D/20250811_A3D_1_T_G\\APO01U1250320.SES\\APOLOC03U1250320.ME0")
    assert (vv == "U1")
    assert True


def test___me0_and_imagesfolder():
    image_me0_dir, loc_me0, sag_me0, ses_me0, vv = process_session.__me0_and_imagesfolder(
        "A3D/20250811_A3D_1_T_G/IAE02U1250320",
        "A3D/20250811_A3D_1_T_G/APO01U1250320.SAG",
        "A3D/20250811_A3D_1_T_G/APO01U1250320.SES",
        "250320")
    assert (image_me0_dir == "A3D/20250811_A3D_1_T_G/IAE02U1250320\\APOIAE02U1250320.ME0_")
    assert (loc_me0 == "A3D/20250811_A3D_1_T_G/APO01U1250320.SES\\APOLOC03U1250320.ME0")
    assert (sag_me0 == "A3D/20250811_A3D_1_T_G/APO01U1250320.SAG\\APOIAE02U1250320.ME0")
    assert (ses_me0 == "A3D/20250811_A3D_1_T_G/APO01U1250320.SES\\APOIAE02U1250320.ME0")
    assert (vv == "U1")
    assert True


def test___list_session_folders():
    images_dir, sag_dir, ses_dir = process_session.__list_session_folders("A3D/20250811_A3D_1_T_G", "250320")
    assert (images_dir == "A3D/20250811_A3D_1_T_G\\IAE02U1250320")
    assert (sag_dir == "A3D/20250811_A3D_1_T_G\\APO01U1250320.SAG")
    assert (ses_dir == "A3D/20250811_A3D_1_T_G\\APO01U1250320.SES")
    assert True


def test_process_session_check():
    assert True

````

## tests\test_revise_duplicated_lines_me0.py

````python
import os
import shutil

import pytest
from src import revise_duplicated_lines_me0


def test_revise_duplicated_lines_me0():
    shutil.copy("A3D/20250811_A3D_2_T_G/APO01U2250310.SAG/APOIAE02U2250310.ME0", ".pytest_cache/test_sag.ME0")
    shutil.copy("A3D/20250811_A3D_2_T_G/APO01U2250310.SES/APOIAE02U2250310.ME0", ".pytest_cache/test_ses.ME0")
    assert (revise_duplicated_lines_me0.revise_duplicated_lines_me0(".pytest_cache/test_sag.ME0") > 0)
    assert (revise_duplicated_lines_me0.revise_duplicated_lines_me0(".pytest_cache/test_ses.ME0") > 0)
    assert(os.path.exists(".pytest_cache/test_sag.ME0.old"))
    assert(os.path.exists(".pytest_cache/test_ses.ME0.old"))
    os.remove(".pytest_cache/test_sag.ME0.old")
    os.remove(".pytest_cache/test_ses.ME0.old")
    os.remove(".pytest_cache/test_sag.ME0")
    os.remove(".pytest_cache/test_ses.ME0")
    assert True


def test_process_me0_ses():
    assert True


def test_process_me0_sag():
    assert True

````

## tests\test_rollback_session.py

````python
import os
import shutil
from pathlib import Path

import pytest
from src import rollback_session, process_session


def test_rename_back_images():
    shutil.rmtree(os.path.dirname(".pytest_cache/A3D"))
    os.makedirs(os.path.dirname(".pytest_cache/A3D"), exist_ok=True)
    shutil.copytree("A3D", ".pytest_cache/A3D", dirs_exist_ok=True)
    assert (process_session.process_session(".pytest_cache/A3D/20250811_A3D_1_T_G", "250320"))
    assert (os.path.exists(".pytest_cache/A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_/renommage.txt"))
    rollback_session.rename_back_images(
        ".pytest_cache/A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_/renommage.txt")
    assert True


def test_rollback_session():
    shutil.rmtree(os.path.dirname(".pytest_cache/A3D"))
    os.makedirs(os.path.dirname(".pytest_cache/A3D"), exist_ok=True)
    shutil.copytree("A3D", ".pytest_cache/A3D", dirs_exist_ok=True)
    assert (process_session.process_session(".pytest_cache/A3D/20250811_A3D_1_T_G", "250320"))
    assert (process_session.process_session(".pytest_cache/A3D/20250811_A3D_1_T_G", "250310"))
    assert (process_session.process_session(".pytest_cache/A3D/20250811_A3D_2_T_G", "250310"))
    assert (os.path.exists(".pytest_cache/A3D/20250811_A3D_1_T_G/IAE02U1250320/APOIAE02U1250320.ME0_/renommage.txt"))
    assert (os.path.exists(".pytest_cache/A3D/20250811_A3D_1_T_G/APO01U1250320.SES/APOIAE02U1250320.ME0.old"))
    assert (os.path.exists(".pytest_cache/A3D/20250811_A3D_1_T_G/APO01U1250310.SES/APOIAE02U1250310.ME0.old"))
    assert (os.path.exists(".pytest_cache/A3D/20250811_A3D_2_T_G/APO01U2250310.SES/APOIAE02U2250310.ME0.old"))
    lines = Path(".pytest_cache/A3D/20250811_A3D_1_T_G/20250811_A3D_1_T_G.txt").read_text().strip().splitlines()
    assert (len(lines) == 2)
    assert (rollback_session.rollback(".pytest_cache/A3D/20250811_A3D_1_T_G", "250320"))
    lines = Path(".pytest_cache/A3D/20250811_A3D_1_T_G/20250811_A3D_1_T_G.txt").read_text().strip().splitlines()
    assert (len(lines) == 1)
    # cleaned = ("\n".join(line for line in lines.splitlines() if line.strip())).splitlines()
    # assert (cleaned.__len__() == 1 + 1)
    lines = Path(".pytest_cache/A3D/20250811_A3D_2_T_G/20250811_A3D_2_T_G.txt").read_text().strip().splitlines()
    assert (len(lines) == 1)
    assert (rollback_session.rollback(".pytest_cache/A3D/20250811_A3D_1_T_G", "250310"))
    assert (rollback_session.rollback(".pytest_cache/A3D/20250811_A3D_2_T_G", "250310"))
    lines = Path(".pytest_cache/A3D/20250811_A3D_1_T_G/20250811_A3D_1_T_G.txt").read_text().strip().splitlines()
    assert (len(lines) == 0)
    lines = Path(".pytest_cache/A3D/20250811_A3D_2_T_G/20250811_A3D_2_T_G.txt").read_text().strip().splitlines()
    assert (len(lines) == 0)
    assert True

````

