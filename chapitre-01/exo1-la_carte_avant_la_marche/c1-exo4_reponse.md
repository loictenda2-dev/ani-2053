
Annotation ligne par ligne — NKMath.jenga

Module : Kernel/Foundation/NKMath (non montré en exemple dans le chapitre)

python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

Confirme que c'est un vrai script Python exécutable, pas un fichier de configuration statique (cohérent avec le chapitre : « Jenga est un programme, pas une configuration »).

python
"""
NKMath — Bibliothèque mathématique fondamentale (C++17)
...
Dépend de NKCore pour les primitives brutes (nk_uint8, etc.)
"""

Docstring qui documente le module en langage humain : rôle, types fournis (NkVec2u, NkVec2i, NkRect, NkVec2f, NkVec3f...), et sa dépendance principale vers NKCore. Convention de lisibilité, sans effet pour Jenga lui-même.

python
from Jenga import *
from jengaconfig import *
from Jenga import * : importe les fonctions du DSL (project, files, links...) — exactement ce que décrit le chapitre.
from jengaconfig import * :  rôle exact incertain — probablement des constantes/config partagées du dépôt (ex. TC_WINDOWS utilisé plus bas). À vérifier en ouvrant jengaconfig.py. ?
python
with project("NKMath"):
    language("C++")
    cppdialect("C++17")
    location(".")

Déclare le projet NKMath. Langage C++, norme C++17. location(".") : les fichiers sont dans le dossier courant (là où se trouve ce .jenga).

python
nkentseudependson(
    ["NKCore", "NKPlatform", "NKContainers", "NKMemory"],
    selfexport="NKMath",
    extra_includes=["src", "pch"],
)

C'est le dependson du chapitre, sous forme de fonction maison du dépôt (« un fichier de projet devenu répétitif a le droit de contenir une fonction — c'est exactement ce que le dépôt a fait avec nkentseudependson »). NKMath doit être construit après NKCore, NKPlatform, NKContainers, NKMemory.

selfexport="NKMath" et extra_includes=["src", "pch"] : paramètres non expliqués dans le chapitre. Hypothèse : exposition du module aux projets qui en dépendent, et ajout de dossiers d'en-têtes supplémentaires — à confirmer dans le code source de cette fonction. ?

python
pchheader("pch/pch.h")
pchsource("pch/pch.cpp")

Déclare l'en-tête précompilé (Precompiled Header) et sa source. Accélère la compilation en pré-compilant les en-têtes communs une seule fois.

python
files([
    "src/NKMath/**.cpp",
    "src/NKMath/**.h",
])

La liste des fichiers sources à compiler, par motif — le rôle « il sait quoi compiler » du chapitre. Tous les .cpp/.h sous src/NKMath/, récursivement (**).

python
objdir("%{wks.location}/Build/Obj/%{cfg.buildcfg}-%{cfg.system}/%{prj.name}")
targetdir("%{wks.location}/Build/Lib/%{cfg.buildcfg}-%{cfg.system}")

Chemins de sortie : où mettre les fichiers objets compilés (Obj) et la bibliothèque finale (Lib). Les variables %{...} s'adaptent à la config et à la plateforme au moment du build.

python
with filter("system:Windows && options:windows-runtime=uwp"):
    objdir(...)
    targetdir(...)

Redéfinit les chemins de sortie spécifiquement pour Windows en runtime UWP, avec un suffixe -uwp pour ne pas mélanger les sorties.

python
with filter("system:Windows && !options:windows-runtime=uwp ..."):
    usetoolchain(TC_WINDOWS)
with filter("system:UWP || ..."):
    usetoolchain("xbox-clang")
with filter("system:macOS"):
    usetoolchain("clang-native")
with filter("system:Android"):
    # Workaround: disable PCH on Android (NDK r27 + clang 18 + libc++)
    pchheader("")
    pchsource("")
    usetoolchain("android-ndk")
with filter("system:HarmonyOS"):
    pchheader("")
    pchsource("")
    usetoolchain("ohos-ndk")
with filter("system:Web"):
    usetoolchain("emscripten")
with filter("system:XboxSeries || system:XboxOne"):
    usetoolchain("xbox-clang")

Série de filtres qui choisissent la chaîne de compilation (toolchain) selon la plateforme cible — « il sait comment appeler le compilateur, les options différentes par plateforme » du chapitre. Les commentaires expliquent pourquoi le PCH est désactivé sur Android/HarmonyOS (limitation du NDK + clang).

 Noms exacts des toolchains ("clang-native", "xbox-clang", "ohos-ndk"...) : probablement des identifiants internes définis ailleurs dans le dépôt — à confirmer avec grep -rn "xbox-clang". ?

python
with filter("config:Debug"):
    defines(["_DEBUG", "DEBUG"])
    optimize("Off")
    symbols(True)
with filter("config:Release"):
    defines(["NDEBUG"])
    optimize("Speed")
    symbols(False)

Deux filtres classiques : en Debug, macros _DEBUG/DEBUG, pas d'optimisation, symboles de débogage conservés. En Release, NDEBUG, optimisation vitesse, pas de symboles.

python
with filter("(system:Linux || system:macOS || (system:Windows && !options:windows-runtime=uwp && !system:XboxSeries && !system:XboxOne)) && !system:Android && !system:iOS || system:Web"):
    with test():
        testfiles(["tests/**.cpp"])

Active les tests unitaires seulement sur desktop (Linux, macOS, Windows non-UWP/non-Xbox) ou Web, en excluant Android et iOS. testfiles([...]) déclare les fichiers de test — c'est le tests du chapitre.

 Logique exacte du filtre : combinaison dense de &&/|| imbriqués — à décortiquer condition par condition pour être sûr de bien la lire.?

Remarque générale

Pas de links dans ce fichier — contrairement à dependson, bien présent. Cohérent avec le fait que NKMath est une bibliothèque de fondations : elle n'a probablement besoin de lier aucune autre bibliothèque, juste d'être construite après ses dépendances.

Récapitulatif des points d'interrogation ???
Rôle exact de from jengaconfig import *
Rôle exact des paramètres selfexport et extra_includes de nkentseudependson
Définition précise des identifiants de toolchain (clang-native, xbox-clang, ohos-ndk, android-ndk, emscripten)
Lecture complète et sûre du filtre combiné qui active les tests
