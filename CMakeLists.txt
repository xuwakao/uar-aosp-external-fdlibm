# Copyright (C) 2025 UAR Project
#
# fdlibm - Freely Distributable LIBM
# IEEE 754 strict math library for StrictMath support
#
# This library provides IEEE 754 compliant math functions used by
# java.lang.StrictMath native implementation.

cmake_minimum_required(VERSION 3.16)

project(fdlibm C)

# ============================================================
# Source Files (from Android.bp)
# ============================================================

set(FDLIBM_SOURCES
    # Kernel functions (internal)
    k_standard.c
    k_rem_pio2.c
    k_cos.c
    k_sin.c
    k_tan.c

    # Core math functions (e_*)
    e_acos.c
    e_acosh.c
    e_asin.c
    e_atan2.c
    e_atanh.c
    e_cosh.c
    e_exp.c
    e_fmod.c
    e_gamma.c
    e_gamma_r.c
    e_hypot.c
    e_j0.c
    e_j1.c
    e_jn.c
    e_lgamma.c
    e_lgamma_r.c
    e_log.c
    e_log10.c
    e_pow.c
    e_rem_pio2.c
    e_remainder.c
    e_scalb.c
    e_sinh.c
    e_sqrt.c

    # Wrapper functions (error handling)
    w_acos.c
    w_acosh.c
    w_asin.c
    w_atan2.c
    w_atanh.c
    w_cosh.c
    w_exp.c
    w_fmod.c
    w_gamma.c
    w_gamma_r.c
    w_hypot.c
    w_j0.c
    w_j1.c
    w_jn.c
    w_lgamma.c
    w_lgamma_r.c
    w_log.c
    w_log10.c
    w_pow.c
    w_remainder.c
    w_scalb.c
    w_sinh.c
    w_sqrt.c

    # Standard functions (s_*)
    s_asinh.c
    s_atan.c
    s_cbrt.c
    s_ceil.c
    s_copysign.c
    s_cos.c
    s_erf.c
    s_expm1.c
    s_fabs.c
    s_finite.c
    s_floor.c
    s_frexp.c
    s_ilogb.c
    s_isnan.c
    s_ldexp.c
    s_lib_version.c
    s_log1p.c
    s_logb.c
    s_matherr.c
    s_modf.c
    s_nextafter.c
    s_rint.c
    s_scalbn.c
    s_signgam.c
    s_significand.c
    s_sin.c
    s_tan.c
    s_tanh.c
)

# ============================================================
# Build Static Library
# ============================================================

add_library(fdlibm STATIC ${FDLIBM_SOURCES})

# C99 standard - specifies less relaxed floating point model
# that does not enable floating point expression contraction
set_target_properties(fdlibm PROPERTIES
    C_STANDARD 99
    C_STANDARD_REQUIRED ON
)

# ============================================================
# Compile Options (from Android.bp)
# ============================================================

target_compile_definitions(fdlibm PRIVATE
    # CRITICAL: This guarantees IEEE 754 core functions are used
    _IEEE_LIBM

    # Endianness - macOS ARM64 and x86_64 are little-endian
    __LITTLE_ENDIAN
)

target_compile_options(fdlibm PRIVATE
    # Disable compiler optimizations that interact badly with this
    # crufty library (see their own admission in 'readme').
    # Without this, we fail StrictMath tests on x86.
    -fno-strict-aliasing

    # Warnings
    -Wno-sign-compare
    -Wno-dangling-else
    -Wno-unknown-pragmas
    -Wno-logical-op-parentheses
    -Wno-sometimes-uninitialized
    -Wno-parentheses
)

# ============================================================
# Include Directory
# ============================================================

target_include_directories(fdlibm PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}
)

# ============================================================
# Installation
# ============================================================

install(TARGETS fdlibm
    ARCHIVE DESTINATION lib
)

install(FILES fdlibm.h
    DESTINATION include
)
