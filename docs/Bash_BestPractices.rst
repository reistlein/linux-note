=======================
Bash Best Pracices
=======================

Target users for this note is system administrators.

-------------------------------------------------------
Bash header
-------------------------------------------------------

.. code-block:: shell
  :linenos:
   
  #!/usr/bin/bash
  # Set magic variables for current FILE & DIR
  __source_dir=$( cd "$( dirname "$(readlink -f "${BASH_SOURCE[0]}" )" )" && pwd )>/dev/null
  __file=$(readlink -f "${BASH_SOURCE[0]}" )
  __filename=$(basename "$__file")
  
  
-------------------------------------------------------
Bash Log
-------------------------------------------------------

.. code:: 

  #-----------
  #Main
  #----------
  function main(){

  echo "[INFO] Process"

  }

  OUTPUT_FILE="$__filename"
  log_filename=$(date +"%Y-%m-%d_%H-%M_${OUTPUT_FILE}.log")

  #Display the generated logs in terminal and outputfile
  main ${HOST} 2>&1 | tee ${log_filename}

-----------------------------------------------
The script exit upon error [#f1]_ &  [#f2]_
-----------------------------------------------

.. code:: 

  set -o errexit  # fail if a command return an exit status != 0
  set -o nounset  # fail if a variable is not set
  set -o pipefail # fail if one command on the pipe failed
  set -o errtrace # Enable the err trap, code will get called when an error is detected
  trap 'echo ERROR: There was an error in ${BASH_SOURCE[0]}:${LINENO}, function ${FUNCNAME:-main context}, details to follow' ERR



.. [#f1] https://linuxhint.com/bash-programming-best-practices/
.. [#f2] https://www.redhat.com/sysadmin/bash-error-handling


.. note::

  This setting shoud be carrefully tested (to avoid false error)
  When the option 'errexit' command that may failed should be in a condition [#]_ or shoud suffix by a or with a condition always true: ``command && rc=$? || rc=$?``
  
  .. code-block:: shell
    :linenos:
    
    if grep -q something /path/to/somefile; then
      do_something  # found
    else
      do_something_else  # not found
    fi

.. [#] https://stackoverflow.com/questions/44080974/if-errexit-is-on-how-do-i-run-a-command-that-might-fail-and-get-its-exit-code
 




-------------------------------------------------------
Debug [#]_
-------------------------------------------------------

.. [#] https://www.redhat.com/sysadmin/error-handling-bash-scripting

.. code::

  _DEBUG="on"
  function DEBUG()
  {
  if [ "$_DEBUG" == "on" ]; then
      $@
  else
      #Do nothing but exit 0
      return 0;
  fi
  }


see Bash get value from property file for example of usage.

-------------------------------------------------------
Bash get value from property file
-------------------------------------------------------

.. code:: 

  #This function allows to get the property from the given CSV file.
  #@param $1 the property to get
  #@param $2 the delimiter to get the field
  #@param $3 the columnNumber to get (see cut field for more details)
  #@param $@ the files to search into
  function get_property_value_bash(){

    local property=$1;
    shift
    local delimiter=$1;
    shift
    local columnNumber=$1;
    shift
    local files=$@;

    DEBUG echo "[DEBUG]  ${FUNCNAME}" >&2
    DEBUG set -x

    if grep --no-messages -h "^${property}" ${files} | cut -s -d "$delimiter" -f ${columnNumber}; then
      return 0; # found
    else
      echo ''
      return 0; # not found
    fi
    DEBUG set +x 
  }



-------------------------------------------------------
UnitTest [#1]_
-------------------------------------------------------

.. [#1] https://github.com/jasonkarns/bats-mock

^^^^^^^^^^^^^^^^^^^^
.gitmodules
^^^^^^^^^^^^^^^^^^^^
.. code:: 

  git submodule add https://github.com/bats-core/bats-core.git test/bats
  git submodule add https://github.com/bats-core/bats-support.git test/test_helper/bats-support
  git submodule add https://github.com/bats-core/bats-assert.git test/test_helper/bats-assert
  git submodule add https://github.com/grayhemp/bats-mock test/test_helper/mocks
  git submodule update --init  #to update the module



^^^^^^^^^^^^^^^^^^^^
Test
^^^^^^^^^^^^^^^^^^^^

.. code:: 
  
  #!bats/bin/bats

  # Set magic variables for current FILE & DIR
  __source_dir=$( cd "$( dirname "$(readlink -f "${BASH_SOURCE[0]}" )" )" && pwd )>/dev/null
  __file=$(readlink -f "${BASH_SOURCE[0]}" )
  __filename=$(basename "${__file}")

  load test_helper.bash

  @test "run sample script" {

    mocked_command="date"
    mock="$(mock_create)"
    mock_path="${mock%/*}" # Parameter expansion to get the folder portion of the temp mock's path
    mock_file="${mock##*/}" # Parameter expansion to get the filename portion of the temp mock's path
    ln -sf "${mock_path}/${mock_file}" "${mock_path}/${mocked_command}"
    PATH="${mock_path}:$PATH" # Putting the stub at the beginning of the PATH so it gets picked up first
    
    mock_set_output "${mock}" "YYYY-MM-DD_hh-mm_ss_sample_getproperties.sh.log" 1
    
    run ${BATS_TEST_DIRNAME}/../src/01_sample_getproperties.sh

    # Cleanup our stub and fixup the PATH
    rm "${mock_path}/${mocked_command}"
    PATH="${PATH/${mock_path}:/}"
    
  }

Run the test 

.. code:: 

  bats/bin/bats . --show-output-of-passing-tests #display output of test (even when the result is ok)

Possibility to integrate the shell test in Jenkins

.. code:: 

  # Delete target folder including junit report and re-create the folder.
  rm -rf target; mkdir -p target/junit-reports
  # Run bash bats test with junit formatter
  test/bats/bin/bats --formatter junit test | tee target/junit-reports/TEST-report.xml;


-------------------------------------------------------
Shell Lint [#]_
-------------------------------------------------------

.. [#] https://github.com/koalaman/shellcheck

Run the shellcheck from shell:

.. code:: 

  lint/shellcheck.exe ../src/01_sample_getproperties.sh


.. note:

  Be caution that some false positive may shown by shellcheck.
  To disable the check a comment should be added more information on each the wiki of shellcheck [#]_
  
.. code:: 

  # We want this to output $PATH without expansion
  # shellcheck disable=SC2016



.. [#] https://www.shellcheck.net/wiki/SC2016
