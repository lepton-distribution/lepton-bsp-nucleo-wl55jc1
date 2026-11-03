# BSP nucleo-wl55jc1 : paramètres hors manifeste (PA-7 Q3), inclus par le CMakeLists.txt racine
# du greffon noyau quand LEPTON_BOARD=nucleo-wl55jc1. XML mklepton : LEPTON_MKCONF du preset.
# Lepton sur le CPU1 (Cortex-M4) seul, CPU2 jamais démarré (décision 2026-10-05).
# DEV_ID 0x497, CPUID 0x410FC241, flash 256 Ko, RDP 0, ESE 0, relus par la sonde (2026-10-05).
set(LEPTON_BOARD_UNAME_MACHINE cortexM4-stm32wlxx)
# nom vu par uname : posé ici, sans entrée dans la table de kernelconf.h (ajout-coeur.md §4)
target_compile_definitions(lepton_options INTERFACE
  __KERNEL_CPU_DEVICE_NAME="${LEPTON_BOARD_UNAME_MACHINE}")

# carte réelle : console série du STLINK-V3 et sonde OpenOCD. Deux cartes identiques sur le banc
# (radio) : la sonde est choisie par son numéro de série. La cfg statique du BSP (champ openocd du
# manifeste, posée par le lecteur dans LEPTON_BOARD_OPENOCD_CFG, autonome depuis ENV-9) est sourcée
# par une cfg générée dans le répertoire de build, qui y ajoute le numéro de série et la remplace
# pour la cible flash et les tests.
set(LEPTON_BOARD_STLINK_SERIAL "" CACHE STRING
    "Numéro de série du STLINK-V3 de la carte (vide : première sonde trouvée)")
set(lepton_wl_cfg_bsp ${LEPTON_BOARD_OPENOCD_CFG})
set(LEPTON_BOARD_OPENOCD_CFG ${CMAKE_BINARY_DIR}/openocd-nucleo-wl55jc1.cfg)
if(LEPTON_BOARD_STLINK_SERIAL)
  set(lepton_wl_serial "adapter serial ${LEPTON_BOARD_STLINK_SERIAL}\n")
else()
  set(lepton_wl_serial "")
endif()
file(WRITE ${LEPTON_BOARD_OPENOCD_CFG}
  "# généré par le CMakeLists.txt du BSP nucleo-wl55jc1\n"
  "source ${lepton_wl_cfg_bsp}\n${lepton_wl_serial}")
set(LEPTON_BOARD_SERIAL_PORT "" CACHE STRING
    "Port série de la console de la carte (ex. /dev/ttyACM0) : active le test de fumée sur carte")

# test radio entre deux cartes (nucleo-wl55jc1.radio, variables LEPTON_RADIO_PEER_*) : répertoire
# tests/ du BSP, champ « tests » du manifeste (TST-2, ENV-32)
