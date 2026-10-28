Covers **5** reports from #4471: R-41202, R-41209, R-41236, R-41344, R-41620.

Dates land exactly one day off, not month/day swapped. The reports point at time zones (same meeting on different days across zones; an invite before the booking). The tenant directory files them under the parser, so the old table counted them there.
