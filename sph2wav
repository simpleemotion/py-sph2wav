import glob
import os
import struct

formats = {'ulaw': 0x07}


def tokenize_header(header, tokens={}):
    for line in header.splitlines():
        parts = line.strip().split(' ')
        if len(parts) == 3:
            tokens[parts[0]] = int(parts[2]) if parts[1] == '-i' else parts[2]
    return tokens


def write_wav_file(f, header, data):
    block_align = header['channel_count'] * header['sample_n_bytes']
    f.write("RIFF")
    f.write(struct.pack('I', 36 + len(data)))
    f.write('WAVE')
    f.write('fmt ')
    f.write(struct.pack('I', 16))
    f.write(struct.pack('H', formats[header['sample_coding']]))
    f.write(struct.pack('H', header['channel_count']))
    f.write(struct.pack('I', header['sample_rate']))
    f.write(struct.pack('I', header['sample_rate'] * block_align))
    f.write(struct.pack('H', block_align))
    f.write(struct.pack('H', header['sample_sig_bits']))
    f.write('data')
    f.write(struct.pack('I', len(data)))
    f.write(data)


def main(files):
    for sph in glob.glob(files):
        print sph
        with open(sph, 'rb') as f:
            header = tokenize_header(f.read(1024))
            data = f.read(header['sample_count'] * header['sample_n_bytes'] * header['channel_count'])
        assert header['channels_interleaved'] == 'TRUE' or header['channel_count'] == 1
        with open(os.path.splitext(sph)[0] + '.wav', 'wb+') as f:
            write_wav_file(f, header, data)


if __name__ == "__main__":
    main('swb1/*.sph')
