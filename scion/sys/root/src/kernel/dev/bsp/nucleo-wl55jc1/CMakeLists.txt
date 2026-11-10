# BSP nucleo-wl55jc1 : paramètres hors manifeste (PA-7 Q3), inclus par le CMakeLists.txt racine
# du greffon noyau quand LEPTON_BOARD=nucleo-wl55jc1. XML mklepton : LEPTON_MKCONF du preset.
# Lepton sur le CPU1 (Cortex-M4) seul, CPU2 jamais démarré (décision 2026-10-05).
# DEV_ID 0x497, CPUID 0x410FC241, flash 256 Ko, RDP 0, ESE 0, relus par la sonde (2026-10-05).
set(LEPTON_BOARD_UNAME_MACHINE cortexM4-stm32wlxx)
# nom vu par uname : posé ici, sans entrée dans la table de kernelconf.h (ajout-coeur.md §4)
target_compile_definitions(lepton_options INTERFACE
  __KERNEL_CPU_DEVICE_NAME="${LEPTON_BOARD_UNAME_MACHINE}")

# carte réelle : console série du STLINK-V3 et sonde OpenOCD. Deux cartes identiques sur le banc
# (radio) : la sonde est choisie par son numéro de série, LEPTON_BOARD_PROBE_SERIAL, commune à tous
# les BSP depuis ENV-45 (lecteur du noyau, cmake/lepton_greffons.cmake : cfg générée
# openocd-nucleo-wl55jc1.cfg dans LEPTON_BOARD_OPENOCD_CFG ; ancien nom LEPTON_BOARD_STLINK_SERIAL
# encore accepté, obsolète).
set(LEPTON_BOARD_SERIAL_PORT "" CACHE STRING
    "Port série de la console de la carte (ex. /dev/ttyACM0) : active le test de fumée sur carte")

# test radio entre deux cartes (nucleo-wl55jc1.radio, variables LEPTON_RADIO_PEER_*) : répertoire
# tests/ du BSP, champ « tests » du manifeste (TST-2, ENV-32)
