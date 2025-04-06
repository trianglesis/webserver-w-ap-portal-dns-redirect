idf_component_register(SRCS "web_portal_main.c"
                    INCLUDE_DIRS ".")

# Note: you must have a partition named the first argument (here it's "littlefs")
# in your partition table csv file.
littlefs_create_partition_image(littlefs ../flash_data FLASH_IN_PROJECT)