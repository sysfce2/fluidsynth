
ENABLE_TESTING()
include ( FluidUnitTest )

# first define the test target, used by the macros below
add_custom_target(check COMMAND ${CMAKE_CTEST_COMMAND} -C $<CONFIG>  --output-on-failure)


## add unit tests here ##
ADD_FLUID_TEST(test_synth_reset_cc)
ADD_FLUID_TEST(test_sample_cache)
ADD_FLUID_TEST(test_sfont_loading)
ADD_FLUID_TEST(test_sample_rate_change)
ADD_FLUID_TEST(test_preset_sample_loading)
ADD_FLUID_TEST(test_preset_pinning)
ADD_FLUID_TEST(test_bug_635)
ADD_FLUID_TEST(test_settings_unregister_callback)
ADD_FLUID_TEST(test_pointer_alignment)
ADD_FLUID_TEST(test_seqbind_unregister)
ADD_FLUID_TEST(test_synth_chorus_reverb)
ADD_FLUID_TEST(test_snprintf)
ADD_FLUID_TEST(test_synth_process)
ADD_FLUID_TEST(test_ct2hz)
ADD_FLUID_TEST(test_sample_validate)
ADD_FLUID_TEST(test_sfont_unloading)
ADD_FLUID_TEST(test_sfont_zone)
ADD_FLUID_TEST(test_seq_event_queue_sort)
ADD_FLUID_TEST(test_seq_scale)
ADD_FLUID_TEST(test_seq_evt_order)
ADD_FLUID_TEST(test_seq_event_queue_remove)
ADD_FLUID_TEST(test_jack_obtaining_synth)
ADD_FLUID_TEST(test_utf8_open)

if( LIBSNDFILE_SUPPORT )
    ADD_FLUID_TEST(test_fast_render)
endif()

ADD_FLUID_TEST_UTIL(dump_sfont)

ADD_FLUID_SF_DUMP_TEST(VintageDreamsWaves-v2.sf2)

if ( LIBSNDFILE_HASVORBIS )
    ADD_FLUID_TEST(test_sf3_sfont_loading)
    ADD_FLUID_SF_DUMP_TEST(VintageDreamsWaves-v2.sf3)
endif ( LIBSNDFILE_HASVORBIS )


# Prepare the manual test suite down here
if(NOT DEFINED GENERAL_USER_GS2)
    add_custom_target(check_manual
        COMMAND ${CMAKE_COMMAND} -E cmake_echo_color --red "In order to run the manual test suite, you need to set variable GENERAL_USER_GS2 to the path of the soundfont.")
else()

    set(IIR_FILTER_RENDER_DIR "${CMAKE_CURRENT_BINARY_DIR}/manual/iir_filter")
    set(AWE32_NRPN_RENDER_DIR "${CMAKE_CURRENT_BINARY_DIR}/manual/awe32_nrpn")
    set(SFSPEC_RENDER_DIR "${CMAKE_CURRENT_BINARY_DIR}/manual/SoundFont-Spec-Test")
    set(PORTAMENTO_RENDER_DIR "${CMAKE_CURRENT_BINARY_DIR}/manual/portamento")
    set(REVERB_RENDER_DIR "${CMAKE_CURRENT_BINARY_DIR}/manual/reverb")
    set(EXCL_RENDER_DIR "${CMAKE_CURRENT_BINARY_DIR}/manual/exclusive_class")

    if(LIBSNDFILE_SUPPORT)
      set(FEXT "wav")
    else()
      set(FEXT "raw")
    endif()

    # Add an empty pseudo target
    add_custom_target(check_manual)

    add_custom_target(create_iir_dir
        COMMAND ${CMAKE_COMMAND} -E make_directory ${IIR_FILTER_RENDER_DIR} ${AWE32_NRPN_RENDER_DIR} ${SFSPEC_RENDER_DIR} ${PORTAMENTO_RENDER_DIR} ${REVERB_RENDER_DIR} ${EXCL_RENDER_DIR}
        VERBATIM)

    add_custom_target(render1415
        COMMAND fluidsynth -R 0 -C 0 -g 1 -F ${IIR_FILTER_RENDER_DIR}/1415_the-nervous-filter.${FEXT} "The Nervous Filter trimmed.mid" ${GENERAL_USER_GS2}
        COMMENT "Rendering testfile of issue 1415"
        DEPENDS fluidsynth create_iir_dir
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/manual/iir_filter/1415_the-nervous-filter/
        VERBATIM
    )

    add_custom_target(render1417
        COMMAND fluidsynth -R 0 -C 0 -g 1 -F ${IIR_FILTER_RENDER_DIR}/1417_filter-envelope-noise.${FEXT} filter-envelope-noise.mid ${GENERAL_USER_GS2}
        COMMENT "Rendering testfile of issue 1417"
        DEPENDS fluidsynth create_iir_dir
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/manual/iir_filter/1417_filter-envelope-noise/
        VERBATIM
    )

    add_custom_target(render1424
        COMMAND fluidsynth -R 0 -C 0 -g 1 -F ${IIR_FILTER_RENDER_DIR}/1424_clicks-on-ModEnv-FilterFc-change.${FEXT} 1424.mid echo_drops.sf2
        COMMENT "Rendering testfile of issue 1424"
        DEPENDS fluidsynth create_iir_dir
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/manual/iir_filter/1424_clicks-on-ModEnv-FilterFc-change/
        VERBATIM
    )

    add_custom_target(render1427
        COMMAND fluidsynth -R 0 -C 0 -g 5 -F ${IIR_FILTER_RENDER_DIR}/1427_high-Q-note-cutoff.${FEXT} high_Q_note_cutoff_test.mid high_Q_note_cutoff_test.sf2
        COMMENT "Rendering testfile of issue 1427"
        DEPENDS fluidsynth create_iir_dir
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/manual/iir_filter/1427_high-Q-note-cutoff/
        VERBATIM
    )


    add_custom_target(renderAltitude
        COMMAND fluidsynth -R 1 -C 1 -g 0.5 -F ${AWE32_NRPN_RENDER_DIR}/Altitude.${FEXT} Altitude.mid Altitude.sf2
        COMMENT "Rendering AWE32 NRPN test file Altitude.mid"
        DEPENDS fluidsynth create_iir_dir
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/manual/awe32_nrpn/Altitude/
        VERBATIM
    )

    add_custom_target(renderFilter
        COMMAND fluidsynth -R 0 -C 0 -g 0.5 -F "${AWE32_NRPN_RENDER_DIR}/The Nervous Filter GenUserGS2.sf2.${FEXT}" "The Nervous Filter.mid" ${GENERAL_USER_GS2}
        COMMAND fluidsynth -R 0 -C 0 -g 0.5 -F "${AWE32_NRPN_RENDER_DIR}/The Nervous Filter 1mgm.sf2.${FEXT}" "The Nervous Filter.mid" "1mgm.sf2"
        COMMAND fluidsynth -R 0 -C 0 -g 0.5 -F "${AWE32_NRPN_RENDER_DIR}/The Nervous Filter GM_Roland.sf2.${FEXT}" "The Nervous Filter.mid" "GM_Roland.sf2"
        COMMENT "Rendering AWE32 NRPN test file The Nervous Filter.mid"
        DEPENDS fluidsynth create_iir_dir
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/manual/awe32_nrpn/Filter/
        VERBATIM
    )

    add_custom_target(renderUplift
        COMMAND fluidsynth -R 1 -C 0 -g 0.7 -F ${AWE32_NRPN_RENDER_DIR}/Uplift-fixed.${FEXT} Uplift-fixed.mid Uplift.sf2
        COMMENT "Rendering AWE32 NRPN test file Uplift.mid"
        DEPENDS fluidsynth create_iir_dir
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/manual/awe32_nrpn/Uplift/
        VERBATIM
    )

    add_custom_target(render1TOWOW
        COMMAND fluidsynth -R 0 -C 0 -g 0.5 -F "${PORTAMENTO_RENDER_DIR}/1TOWOW_reduced_to_A3_C4.${FEXT}" "1TOWOW_reduced_to_A3_C4.mid" ${GENERAL_USER_GS2}
        COMMAND fluidsynth -R 0 -C 0 -g 0.5 -F "${PORTAMENTO_RENDER_DIR}/1TOWOW.${FEXT}" "1TOWOW.MID" ${GENERAL_USER_GS2}
        COMMENT "Rendering Portamento tests"
        DEPENDS fluidsynth create_iir_dir
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/manual/portamento/
        VERBATIM
    )

    add_custom_target(renderDescent8
        COMMAND fluidsynth -R 1 -C 1 -g 0.5 -F ${PORTAMENTO_RENDER_DIR}/Game08.${FEXT} Game08.mid ${GENERAL_USER_GS2}
        COMMENT "Rendering Descent Game 8"
        DEPENDS fluidsynth create_iir_dir
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/manual/portamento/
        VERBATIM
    )

    add_custom_target(renderSfSpecTest
        COMMAND fluidsynth -R 1 -C 1 -g 0.5 -F ${SFSPEC_RENDER_DIR}/sf_spec_test.${FEXT} sf_spec_test.mid sf_spec_test.sf2
        COMMENT "Rendering Christian Collins' SF2 spec test"
        DEPENDS fluidsynth create_iir_dir
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/manual/SoundFont-Spec-Test/
        VERBATIM
    )

    add_custom_target(render1455
        COMMAND fluidsynth -R 1 -C 0 -g 0.5 -o synth.reverb.width=0.0 -F ${REVERB_RENDER_DIR}/mrbumpys_rev_width_test_000.0.${FEXT} mrbumpys_rev_width_test.mid ${GENERAL_USER_GS2}
        COMMAND fluidsynth -R 1 -C 0 -g 0.5 -o synth.reverb.width=0.5 -F ${REVERB_RENDER_DIR}/mrbumpys_rev_width_test_000.5.${FEXT} mrbumpys_rev_width_test.mid ${GENERAL_USER_GS2}
        COMMAND fluidsynth -R 1 -C 0 -g 0.5 -o synth.reverb.width=1.0 -F ${REVERB_RENDER_DIR}/mrbumpys_rev_width_test_001.0.${FEXT} mrbumpys_rev_width_test.mid ${GENERAL_USER_GS2}
        COMMAND fluidsynth -R 1 -C 0 -g 0.5 -o synth.reverb.width=5.0 -F ${REVERB_RENDER_DIR}/mrbumpys_rev_width_test_005.0.${FEXT} mrbumpys_rev_width_test.mid ${GENERAL_USER_GS2}
        COMMAND fluidsynth -R 1 -C 0 -g 0.5 -o synth.reverb.width=10  -F ${REVERB_RENDER_DIR}/mrbumpys_rev_width_test_010.0.${FEXT} mrbumpys_rev_width_test.mid ${GENERAL_USER_GS2}
        COMMAND fluidsynth -R 1 -C 0 -g 0.5 -o synth.reverb.width=50  -F ${REVERB_RENDER_DIR}/mrbumpys_rev_width_test_050.0.${FEXT} mrbumpys_rev_width_test.mid ${GENERAL_USER_GS2}
        COMMAND fluidsynth -R 1 -C 0 -g 0.5 -o synth.reverb.width=100 -F ${REVERB_RENDER_DIR}/mrbumpys_rev_width_test_100.0.${FEXT} mrbumpys_rev_width_test.mid ${GENERAL_USER_GS2}
        COMMENT "Rendering Christian Collins' reverb test issue 1455"
        DEPENDS fluidsynth create_iir_dir
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/manual/reverb/
        VERBATIM
    )

    set(DK64SF2 "../sf2/ANMP-data/soundfonts/N64/DK64.sf2")
    add_custom_target(renderDK64JJU
        COMMAND fluidsynth -R 1 -C 0 -g 0.5 -o synth.reverb.width=0.0 -F ${REVERB_RENDER_DIR}/DK64_sparse04_000.0.${FEXT} DK64_sparse04.mid ${DK64SF2}
        COMMAND fluidsynth -R 1 -C 0 -g 0.5 -o synth.reverb.width=0.5 -F ${REVERB_RENDER_DIR}/DK64_sparse04_000.5.${FEXT} DK64_sparse04.mid ${DK64SF2}
        COMMAND fluidsynth -R 1 -C 0 -g 0.5 -o synth.reverb.width=1.0 -F ${REVERB_RENDER_DIR}/DK64_sparse04_001.0.${FEXT} DK64_sparse04.mid ${DK64SF2}
        COMMAND fluidsynth -R 1 -C 0 -g 0.5 -o synth.reverb.width=5.0 -F ${REVERB_RENDER_DIR}/DK64_sparse04_005.0.${FEXT} DK64_sparse04.mid ${DK64SF2}
        COMMAND fluidsynth -R 1 -C 0 -g 0.5 -o synth.reverb.width=10  -F ${REVERB_RENDER_DIR}/DK64_sparse04_010.0.${FEXT} DK64_sparse04.mid ${DK64SF2}
        COMMAND fluidsynth -R 1 -C 0 -g 0.5 -o synth.reverb.width=50  -F ${REVERB_RENDER_DIR}/DK64_sparse04_050.0.${FEXT} DK64_sparse04.mid ${DK64SF2}
        COMMAND fluidsynth -R 1 -C 0 -g 0.5 -o synth.reverb.width=100 -F ${REVERB_RENDER_DIR}/DK64_sparse04_100.0.${FEXT} DK64_sparse04.mid ${DK64SF2}
        COMMENT "Praise Grant Kirkhope!"
        DEPENDS fluidsynth create_iir_dir
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/manual/reverb/
        VERBATIM
    )

    add_custom_target(renderExcl
        COMMAND fluidsynth -R 0 -C 0 -g 1.4 -F "${EXCL_RENDER_DIR}/exclusive class cutoff speed.${FEXT}" "exclusive class cutoff speed.mid" "exclusive class cutoff speed.sf2"
        COMMAND fluidsynth -R 0 -C 0 -g 1.4 -F "${EXCL_RENDER_DIR}/MIDInotecut.${FEXT}" "MIDInotecut.mid" ${GENERAL_USER_GS2}
        COMMAND fluidsynth -R 0 -C 0 -g 1.4 -F "${EXCL_RENDER_DIR}/percussion note cutoff.${FEXT}" "percussion note cutoff.mid" ${GENERAL_USER_GS2}
        COMMAND fluidsynth -R 0 -C 0 -g 1.4 -F "${EXCL_RENDER_DIR}/percussion-test.${FEXT}" "percussion-test.mid" ${GENERAL_USER_GS2}
        COMMAND fluidsynth -R 0 -C 0 -g 0.6 -F "${EXCL_RENDER_DIR}/e1m1_GeneralUser2.${FEXT}" "e1m1.mid" ${GENERAL_USER_GS2}
        COMMAND fluidsynth -R 0 -C 0 -g 0.6 -F "${EXCL_RENDER_DIR}/e1m1_gzdoom.${FEXT}" "e1m1.mid" "../sf2/gzdoom.sf2"
        COMMENT "Rendering exclusive class cutoff tests"
        DEPENDS fluidsynth create_iir_dir
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/manual/exclusive_class/
        VERBATIM
    )

    # Add a dependency so that rendering targets depends on check_manual
    add_dependencies(check_manual render1415)
    add_dependencies(check_manual render1417)
    add_dependencies(check_manual render1424)
    add_dependencies(check_manual render1427)
    add_dependencies(check_manual renderAltitude)
    add_dependencies(check_manual renderFilter)
    add_dependencies(check_manual renderUplift)
    add_dependencies(check_manual render1TOWOW)
    add_dependencies(check_manual renderDescent8)
    add_dependencies(check_manual renderSfSpecTest)
    add_dependencies(check_manual render1455)
    add_dependencies(check_manual renderDK64JJU)
    add_dependencies(check_manual renderExcl)

endif()
