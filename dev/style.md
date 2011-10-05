# ----------------------------------------------------------------------
# STYLE GUIDE
# ----------------------------------------------------------------------
# avoid changing global state inside functions; prefer to return echo'd
#        values or set indirect variable names (not always possible)
# Global_Functions_Camel_Case_No_Leading_Underscore
# _Local_Functions_Camel_Case_o_Leading_Underscore
# GLOBALS in all caps
# globals that used to retrieve results from functions (pseudo local)
#        all lower case; should be unset after use this temporary use
# _local variables, lowercase leading underscore
# ASSOCIATIVE_ARRAYS_[NOTE_FINAL_UNDERSCORE]
# bracket all ${variables} for consistency

# ----------------------------------------------------------------------
# ENFORCE STYLES
# ----------------------------------------------------------------------
# scan for _locals outside of a function
# scan for _Local_Functions outside of a function
# scan for temp_globals that aren't unset
# scan for Camel_Case in function declarations
