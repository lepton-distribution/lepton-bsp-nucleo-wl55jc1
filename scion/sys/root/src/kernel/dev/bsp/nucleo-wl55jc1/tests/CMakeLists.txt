# Tests du BSP nucleo-wl55jc1 (TST-2, PA-51 d'ENV-32) : répertoire désigné par le champ « tests »
# du manifeste, ajouté par lepton_greffons_tests (lepton_greffons.cmake du greffon noyau) quand
# LEPTON_BOARD=nucleo-wl55jc1. Nom préfixé « nucleo-wl55jc1. », étiquettes greffon:, arch:,
# backend: ajoutées par lepton_greffon_test. Variables de la carte (LEPTON_BOARD_SERIAL_PORT,
# LEPTON_BOARD_OPENOCD_CFG, LEPTON_BOARD_OPENOCD_CFG_BSP) posées par le lecteur du noyau et le
# CMakeLists.txt du BSP.
#
# radio : test radio entre deux cartes (labels board et radio, radio.py) ; la même image est
# flashée sur cette carte et sur la carte paire, désignée par son STLINK-V3 et sa console ; vides :
# le test n'est pas créé.
set(LEPTON_RADIO_PEER_STLINK_SERIAL "" CACHE STRING
    "Numéro de série du STLINK-V3 de la carte paire (test radio)")
set(LEPTON_RADIO_PEER_SERIAL_PORT "" CACHE STRING
    "Console de la carte paire (test radio, ex. /dev/ttyACM1)")
if(LEPTON_BOARD_SERIAL_PORT AND LEPTON_RADIO_PEER_STLINK_SERIAL AND LEPTON_RADIO_PEER_SERIAL_PORT)
  find_program(LEPTON_OPENOCD openocd)
  find_package(Python3 REQUIRED COMPONENTS Interpreter)
  set(lepton_wl_peer_cfg ${CMAKE_BINARY_DIR}/openocd-nucleo-wl55jc1-paire.cfg)
  file(WRITE ${lepton_wl_peer_cfg}
    "# généré par le CMakeLists.txt du BSP nucleo-wl55jc1 (carte paire du test radio)\n"
    "source ${LEPTON_BOARD_OPENOCD_CFG_BSP}\nadapter serial ${LEPTON_RADIO_PEER_STLINK_SERIAL}\n")
  lepton_greffon_test(NAME radio
    COMMAND ${Python3_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/radio.py
            --tests-noyau ${LEPTON_TESTS_NOYAU}
            --port-a ${LEPTON_BOARD_SERIAL_PORT} --port-b ${LEPTON_RADIO_PEER_SERIAL_PORT}
            --reset-a "${LEPTON_OPENOCD} -f ${LEPTON_BOARD_OPENOCD_CFG} -c \"program $<TARGET_FILE:lepton> verify reset exit\""
            --reset-b "${LEPTON_OPENOCD} -f ${lepton_wl_peer_cfg} -c \"program $<TARGET_FILE:lepton> verify reset exit\""
            --log ${CMAKE_BINARY_DIR}/board_radio.log
    LABELS board radio
    PROPERTIES TIMEOUT 300 RUN_SERIAL TRUE ENVIRONMENT PYTHONDONTWRITEBYTECODE=1)
endif()
