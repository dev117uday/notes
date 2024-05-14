# Custom Indexes

**Example :**&#x20;

CREATE a index for social security number , which is the the format of 1111-222-nnnn, you have to index the last 4 characters

```sql
CREATE TABLE if not exists ssn
(
    ssn text
);

INSERT INTO ssn (ssn)
values ('111-11-0100'),
       ('222-22-0120'),
       ('333-33-0140'),
       ('444-44-0160');

select *
from ssn;

explain
select *
from ssn;

CREATE OR REPLACE FUNCTION FN_FIX_SSN(TEXT) RETURNS text AS
$$
BEGIN
    return substring($1, 8) || replace(substring($1, 1, 7), '-', '');

end ;
$$ LANGUAGE plpgsql IMMUTABLE;

SELECT ssn, FN_FIX_SSN(ssn)
from ssn;

drop function fn_ssn_compare cascade;

CREATE OR REPLACE FUNCTION fn_ssn_compare(text,text) returns int as
$$
    BEGIN
        if FN_FIX_SSN($1) < FN_FIX_SSN($2) then return -1;
        elseif FN_FIX_SSN($1) > FN_FIX_SSN($2) then return 1;
        else
            return 0;
        end if;

    end;
$$ language plpgsql immutable;

CREATE OPERATOR CLASS op_class_ssn_ops1
FOR TYPE text USING btree
as
        OPERATOR 1 <,
        OPERATOR 2 <=,
        OPERATOR 3 =,
        OPERATOR 4 >=,
        operator 5 >,
        function 1 fn_ssn_compare(text,text);

CREATE INDEX idx_ssn on ssn(ssn op_class_ssn_ops1);

explain
select *
from ssn where ssn.ssn = '222-22-0120';

set enable_seqscan = 'off';
show enable_seqscan;

```
