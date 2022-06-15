# 15 June 2022

I have figured out why the plane system keeps logging checksum errors when
communicating with Pixhawks that have recent firmware: there are new messages,
such as `MCU_STATUS`, that were not present in the version of the XML files that
is embedded into this repo (these are from 8 June 2020). 

I tried to simply update the Git submodule, but the very next commit
(`cc1833429fae240d234464668602a5656a0dcb2e`) in the `mavlink` repo breaks
everything. I'm pretty sure that this is because of lines such as line 6584 in
`common.xml`: 

```xml
      <field type="uint16_t[4]" name="failure_flags" enum="ESC_FAILURE_FLAGS" display="bitmask">Bitmap of ESC failure flags.</field>
```

Where we have bitflags on array types, which causes the Rust code generation to
go haywire. In order to upgrade anything, we need to find a way to support
bitflags on array types (my idea is to just convert them to integers, ex.:
`uint16_t[4] -> u64`).
