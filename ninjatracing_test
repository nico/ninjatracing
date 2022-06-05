#!/usr/bin/env python
# Copyright 2018 Nico Weber
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

import ninjatracing
import unittest

try:
    from StringIO import StringIO
except ImportError:
    from io import StringIO

class TestNinjaTracing(unittest.TestCase):

    def test_simple(self):
        log = StringIO("# ninja log v5\n"
                       "100\t200\t0\tmy_output\tdeadbeef\n"
                       "50\t120\t0\tmy_first_output\t0afef\n")
        dicts = list(ninjatracing.log_to_dicts(log, 42, {'showall': True}))
        expected = [{
                'name': 'my_output', 'cat': 'targets', 'ph': 'X',
                'ts': 100000, 'dur': 100000, 'pid': 42, 'tid': 0,
                'args': {}
                }, {
                'name': 'my_first_output', 'cat': 'targets', 'ph': 'X',
                'ts': 50000, 'dur': 70000, 'pid': 42, 'tid': 1,
                'args': {}
                },
            ]
        self.assertEqual(expected, dicts)

    def test_last_only(self):
        # Test the behavior without --showall.
        log = StringIO("# ninja log v5\n"
                       "100\t200\t0\tmy_output\tdeadbeef\n"
                       "50\t120\t0\tmy_first_output\t0afef\n")
        dicts = list(ninjatracing.log_to_dicts(log, 42, {'showall': False}))
        expected = [{
                'name': 'my_first_output', 'cat': 'targets', 'ph': 'X',
                'ts': 50000, 'dur': 70000, 'pid': 42, 'tid': 0,
                'args': {}
                },
            ]
        self.assertEqual(expected, dicts)

    def test_multiple_outputs(self):
        # Both lines here have the same command hash and the same start
        # and end times, meaning they were produced by the same command.
        log = StringIO("# ninja log v5\n"
                       "100\t200\t0\toutput\tdeadbeef\n"
                       "100\t200\t0\tother_output\tdeadbeef\n")
        dicts = list(ninjatracing.log_to_dicts(log, 42, {'showall': True}))
        expected = [{
                'name': 'output, other_output', 'cat': 'targets', 'ph': 'X',
                'ts': 100000, 'dur': 100000, 'pid': 42, 'tid': 0,
                'args': {}
                },
            ]
        self.assertEqual(expected, dicts)

    def test_trace(self):
        # Simple test for converting time trace to dictionaries. Tests removing
        # incomplete, "Total" and short events, replacing tid and pid and
        # adjusting timestamp.
        trace = StringIO('{ "traceEvents": ['
                       '{ "dur": 1500, "name": "LongEvent", "ph": "X", "pid": 1, "tid": 12345, "ts": 1000 },'
                       '{ "dur": 500, "name": "TooShort", "ph": "X", "pid": 1, "tid": 12345, "ts": 1000 },'
                       '{ "args": { "avg ms": 1, "count": 2 }, "dur": 1111, "name": "Total Count", "ph": "X", "pid": 1, "tid": 12345, "ts": 0 },'
                       '{ "args": { "name": "clang" }, "cat": "", "name": "process_name", "ph": "M", "pid": 1, "tid": 0, "ts": 0 }'
                       ']}')
        target = ninjatracing.Target(5, 10)
        dicts = list(ninjatracing.trace_to_dicts(target, trace, {'granularity': 1000}, 42, 5))
        expected = [{
            'dur': 1500, 'name': 'LongEvent', 'ph': 'X', 'pid': 42, 'tid': 5, 'ts': 6000},
            ]
        self.assertEqual(expected, dicts)

    def test_comments(self):
        log = StringIO("# ninja log v5\n"
                       "#\n"
                       "100\t200\t0\tmy_output\tdeadbeef\n"
                       "# 100\t666\t0\tignored_output\tdeadbeef\n"
                       "50\t120\t0\tmy_first_output\t0afef\n"
                       "# lastline")
        dicts = list(ninjatracing.log_to_dicts(log, 42, {'showall': True}))
        expected = [{
                'name': 'my_output', 'cat': 'targets', 'ph': 'X',
                'ts': 100000, 'dur': 100000, 'pid': 42, 'tid': 0,
                'args': {}
                }, {
                'name': 'my_first_output', 'cat': 'targets', 'ph': 'X',
                'ts': 50000, 'dur': 70000, 'pid': 42, 'tid': 1,
                'args': {}
                },
            ]
        self.assertEqual(expected, dicts)

if __name__ == '__main__':
    unittest.main()
