# Copyright (c) Microsoft Corporation.
# Licensed under the MIT License.

set(SOURCES
    startup/system_stm32l4xx.c
    startup/tx_initialize_low_level.S
    stm32cubel4/stm32l4xx_hal_msp.c
    stm32cubel4/stm32l4xx_it.c
    legacy/mqtt.c
    azure_config.h
    board_init.c
    console.c
    main.c
    nx_client.c
    stm_networking.c

    # ipc sample
    ipc/ulib_sample.h
    ipc/ulib_sample.c
    # ipc/common/cipher_1_interface.h
    # ipc/consumers/my_consumer.h
    # ipc/consumers/my_consumer.c
    # ipc/consumers/wrappers/cipher_1_wrapper.h
    # ipc/producers/cipher_v1i1/cipher_v1i1.h
    # ipc/producers/cipher_v1i1/cipher_v1i1.c
    # ipc/producers/cipher_v1i1/interfaces/cipher_v1i1_interface.h
    # ipc/producers/cipher_v1i1/interfaces/cipher_v1i1_interface.c
    # ipc/producers/cipher_v2i1/cipher_v2i1.h
    # ipc/producers/cipher_v2i1/cipher_v2i1.c
    # ipc/producers/cipher_v2i1/interfaces/cipher_v2i1_interface.h
    # ipc/producers/cipher_v2i1/interfaces/cipher_v2i1_interface.c

)

function(create_target TARGET STARTUP LINKER)
    add_executable(${TARGET}
        ${SOURCES}
        ${STARTUP}
    )

    target_link_libraries(${TARGET}
        azrtos::threadx
        azrtos::netxduo

        stm32cubel4
        netx_driver
        app_common
        jsmn

        azure_ulib_c
    )

    target_link_options(${TARGET}
        PUBLIC 
            -T${LINKER} -Wl,-Map=${TARGET}.map
    )

    set_target_properties(${TARGET}
        PROPERTIES 
            LINK_DEPENDS ${LINKER}
            SUFFIX ".elf"
    )

    target_include_directories(${TARGET} 
        PUBLIC 
            ${PROJECT_SOURCE_DIR}/app
            ${PROJECT_SOURCE_DIR}/app/ipc
            ${PROJECT_SOURCE_DIR}/app/ipc/common
            ${PROJECT_SOURCE_DIR}/app/ipc/consumers
            ${PROJECT_SOURCE_DIR}/app/ipc/wrappers
            ${PROJECT_SOURCE_DIR}/app/ipc/producers
            ${PROJECT_SOURCE_DIR}/app/ipc/producers/cipher_v1i1
            ${PROJECT_SOURCE_DIR}/app/ipc/producers/cipher_v1i1/interfaces
            ${PROJECT_SOURCE_DIR}/app/ipc/producers/cipher_v2i1
            ${PROJECT_SOURCE_DIR}/app/ipc/producers/cipher_v2i1/interfaces
    )

    create_bin_output(${TARGET})
    firmware_size(${TARGET})
endfunction()

create_target("stm32l475_azure_iot" "startup/startup_stm32l475xx.s" "${CMAKE_CURRENT_SOURCE_DIR}/startup/STM32L475VGTx_FLASH.ld")
create_target("stm32l4S5_azure_iot" "startup/startup_stm32l4s5xx.s" "${CMAKE_CURRENT_SOURCE_DIR}/startup/STM32L4S5VITx_FLASH.ld")
