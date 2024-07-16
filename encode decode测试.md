```shell
mo_ctl stop

mo_ctl uninstall

mo_ctl set_conf MO_GIT_URL="https://githubfast.com/matrixorigin/matrixone.git"

mo_ctl set_conf MO_GIT_URL="git@github.com:charleschile/matrixone.git"

mo_ctl deploy feature/encode-decode

mo_ctl deploy feature/aes-encode-decode

mo_ctl start

mo_ctl connect

./run.sh -p /Users/charles/Desktop/codes/matrixone/matrixone/test/distributed/cases/function/func_decode_encode.sql


```


```mysql
show databases;

create database if not exists test_encode_decode;

use test_encode_decode;

CREATE TABLE IF NOT EXISTS test_encode_decode_table (
    id INT AUTO_INCREMENT PRIMARY KEY,
    original_data VARCHAR(255),
    encoded_data VARCHAR(255)
);



INSERT INTO test_encode_decode_table (original_data, encoded_data)
VALUES ('HelloWorld', ENCODE('HelloWorld', 'password'));

INSERT INTO test_encode_decode_table (original_data, encoded_data)
VALUES ('matrixone', ENCODE('matrixone', 'password'));

INSERT INTO test_encode_decode_table (original_data, encoded_data)
VALUES ('HelloWorld_ps', ENCODE('HelloWorld_ps', 'ps'));

INSERT INTO test_encode_decode_table (original_data, encoded_data)
VALUES ('matrixone_ps', ENCODE('matrixone_ps', 'ps'));

SELECT id, original_data, encoded_data FROM test_encode_decode_table;

SELECT id, original_data, DECODE(encoded_data, 'password') AS decoded_data
FROM test_encode_decode_table;

SELECT id, original_data, DECODE(encoded_data, 'ps') AS decoded_data
FROM test_encode_decode_table;
```


```mysql
CREATE TABLE IF NOT EXISTS test_encode_decode_table1 (
    id INT AUTO_INCREMENT PRIMARY KEY,
    original_data VARCHAR(255),
    encoded_data blob
);



INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('', ENCODE('', ''));

INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('MatrixOne', ENCODE('MatrixOne', '1234567890123456'));


INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('MatrixOne', ENCODE('MatrixOne', 'asdfjasfwefjfjkj'));


INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('MatrixOne123', ENCODE('MatrixOne123', '123456789012345678901234'));

INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('MatrixOne#%$%^', ENCODE('MatrixOne#%$%^', '*^%YTu1234567'));


INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('MatrixOne', ENCODE('MatrixOne', ''));

INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('分布式データベース', ENCODE('分布式データベース', 'pass1234@#$%%^^&'));

INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('分布式データベース', ENCODE('分布式データベース', '分布式7782734adgwy1242'));

INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('MatrixOne', ENCODE('MatrixOne', '密匙'));

INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('MatrixOne数据库', ENCODE('MatrixOne数据库', '数据库passwd12345667'));

SELECT id, original_data, encoded_data FROM test_encode_decode_table1;


```


```mysql

CREATE TABLE IF NOT EXISTS test_encode_decode_table1 (
    id INT AUTO_INCREMENT PRIMARY KEY,
    original_data VARCHAR(255),
    encoded_data blob
);



INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('', DECODE('', ''));

INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('MatrixOne', DECODE('MatrixOne', '1234567890123456'));


INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('MatrixOne', DECODE('MatrixOne', 'asdfjasfwefjfjkj'));


INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('MatrixOne123', DECODE('MatrixOne123', '123456789012345678901234'));

INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('MatrixOne#%$%^', DECODE('MatrixOne#%$%^', '*^%YTu1234567'));


INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('MatrixOne', DECODE('MatrixOne', ''));

INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('分布式データベース', DECODE('分布式データベース', 'pass1234@#$%%^^&'));

INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('分布式データベース', DECODE('分布式データベース', '分布式7782734adgwy1242'));

INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('MatrixOne', DECODE('MatrixOne', '密匙'));

INSERT INTO test_encode_decode_table1 (original_data, encoded_data)
VALUES ('MatrixOne数据库', DECODE('MatrixOne数据库', '数据库passwd12345667'));

SELECT id, original_data, encoded_data FROM test_encode_decode_table1;



```


```sql
SELECT DECODE(ENCODE('Hello, World!', 'mysecretkey'), 'mysecretkey') AS Result;

SELECT DECODE(UNHEX('D51ED05B10610A7CD54B3D0398E7B4536D57D78084A7F6E6F27C3B'), '分布式7782734adgwy1242') AS Result FROM test_encode_decode_table1;


SELECT HEX(ENCODE('分布式データベース', '分布式7782734adgwy1242'));
```

// data structs for encoding and decoding
type randStruct struct {
	seed1       uint32
	seed2       uint32
	maxValue    uint32
	maxValueDbl float64
}

type sqlCrypt struct {
	rand    randStruct
	orgRand randStruct

	decodeBuff [256]byte
	encodeBuff [256]byte
	shift      uint32
}

// helper funcs for encoding and decoding
func (rs *randStruct) randomInit(password []byte, length int) {
	var nr, add, nr2, tmp uint32
	nr = 1345345333
	add = 7
	nr2 = 0x12345671

	for i := 0; i < length; i++ {
		pswChar := password[i]
		if pswChar == ' ' || pswChar == '\t' {
			continue
		}
		tmp = uint32(pswChar)
		nr ^= (((nr & 63) + add) * tmp) + (nr << 8)
		nr2 += (nr2 << 8) ^ nr
		add += tmp
	}

	seed1 := nr & ((uint32(1) << 31) - uint32(1))
	seed2 := nr2 & ((uint32(1) << 31) - uint32(1))

	rs.maxValue = 0x3FFFFFFF
	rs.maxValueDbl = float64(rs.maxValue)
	rs.seed1 = seed1 % rs.maxValue
	rs.seed2 = seed2 % rs.maxValue
}

func (rs *randStruct) myRand() float64 {
	rs.seed1 = (rs.seed1*3 + rs.seed2) % rs.maxValue
	rs.seed2 = (rs.seed1 + rs.seed2 + 33) % rs.maxValue
	return float64(rs.seed1) / rs.maxValueDbl
}

func (sc *sqlCrypt) init(password []byte, length int) {
	sc.rand.randomInit(password, length)
	for i := 0; i <= 255; i++ {
		sc.decodeBuff[i] = byte(i)
	}
	for i := 0; i <= 255; i++ {
		idx := uint32(sc.rand.myRand() * 255.0)
		a := sc.decodeBuff[idx]
		sc.decodeBuff[idx] = sc.decodeBuff[i]
		sc.decodeBuff[i] = a
	}

	for i := 0; i <= 255; i++ {
		sc.encodeBuff[sc.decodeBuff[i]] = byte(i)
	}
	sc.orgRand = sc.rand
	sc.shift = 0
}

func (sc *sqlCrypt) encode(str []byte, length int) {
	for i := 0; i < length; i++ {
		sc.shift ^= uint32(sc.rand.myRand() * 255.0)
		idx := uint32(str[i])
		str[i] = sc.encodeBuff[idx] ^ byte(sc.shift)
		sc.shift ^= idx
	}
}

func (sc *sqlCrypt) decode(str []byte, length int) {
	for i := 0; i < length; i++ {
		sc.shift ^= uint32(sc.rand.myRand() * 255.0)
		idx := uint32(str[i] ^ byte(sc.shift))
		str[i] = sc.decodeBuff[idx]
		sc.shift ^= uint32(str[i])
	}
}

// encode function encrypts a string, returns a binary string of the same length of the original string.
// https://dev.mysql.com/doc/refman/5.7/en/encryption-functions.html#function_encode
func encodeToBytes(data []byte, key []byte, null bool, rs *vector.FunctionResult[types.Varlena]) error {
	if null {
		return rs.AppendMustNullForBytesResult()
	}

	var sc sqlCrypt
	sc.init(key, len(key))
	sc.encode(data, len(data))

	return rs.AppendMustBytesValue(data)
}

func Encode(parameters []*vector.Vector, result vector.FunctionResultWrapper, proc *process.Process, length int, selectList *FunctionSelectList) error {
	source := vector.GenerateFunctionStrParameter(parameters[0])
	key := vector.GenerateFunctionStrParameter(parameters[1])
	rs := vector.MustFunctionResult[types.Varlena](result)

	rowCount := uint64(length)
	for i := uint64(0); i < rowCount; i++ {
		data, nullData := source.GetStrValue(i)
		keyData, nullKey := key.GetStrValue(i)
		if err := encodeToBytes(data, keyData, nullData || nullKey, rs); err != nil {
			return err
		}
	}

	return nil
}

// decode function decodes an encoded string and returns the original string
// https://dev.mysql.com/doc/refman/5.7/en/encryption-functions.html#function_decode
func decodeFromBytes(data []byte, key []byte, null bool, rs *vector.FunctionResult[types.Varlena]) error {
	if null {
		return rs.AppendMustNullForBytesResult()
	}
	var sc sqlCrypt
	sc.init(key, len(key))
	sc.decode(data, len(data))
	return rs.AppendMustBytesValue(data)
}

func Decode(parameters []*vector.Vector, result vector.FunctionResultWrapper, proc *process.Process, length int, selectList *FunctionSelectList) error {
	source := vector.GenerateFunctionStrParameter(parameters[0])
	key := vector.GenerateFunctionStrParameter(parameters[1])
	rs := vector.MustFunctionResult[types.Varlena](result)

	rowCount := uint64(length)
	for i := uint64(0); i < rowCount; i++ {
		data, nullData := source.GetStrValue(i)
		keyData, nullKey := key.GetStrValue(i)
		if err := decodeFromBytes(data, keyData, nullData || nullKey, rs); err != nil {
			return err
		}
	}

	return nil
}



SELECT HEX(ENCODE('MatrixOne数据库', '数据库passwd12345667'));

SELECT ENCODE('mytext','mykeystring');

![[Pasted image 20240706213131.png]]



![[Pasted image 20240706213306.png]]