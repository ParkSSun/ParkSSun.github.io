1. ls -l shows file structure
-> -rwxrwxrwx LinkNumber owner size date time filename
-> -(file type)rwx(owner commission)rwx(group commission)rwx(others commission)(file commission)
-> r(read)w(write)x(execution)
-> file type (- : normal file(ASCII Type text file), b : block device, c : character device, d : directory, l : symbolic link-directly go type @window)
-> b: block device (use buffer - disk, cd driver, random access possible)
-> c : character device (not use buffer - mouse, keyboard, etc.. sequencer process)
-> Linux see devices as file!

2. Link of file
- inode : has file information
- when we create file, inode also is created
- Thus, all files and directories has each inode
- inode list : inode information table
- Types : Hard Link, Soft Linkw
- HardLink
    - cmd :
        - for Hard link : ln aaa aaa2
    - characteristic :
        - use same inode
        - if original file is changed, hard link also changed
        - if original file is deleted, hard link is not deleted
        - if original file is moved, hard link is not moved
        - hard link is managed with link number.
        - thus, link number is not zero, the data block will be remained.
        - the difference with file copy(cp) is that memory space is not used @ hard link
        - directory link is impossible
    - But not used in different file system!
- Symbolic Link
    -cmd :
        - ln -s aaa aaa2
    -characteristic :
        - when symbolic link is maked, new inode is created and this node is connected with original file's inode which is connected with data block
        - so if original file is deleted or moved, the inode of original file is also deleted or moved, so relationship between inode of symbolic link and inode of original file is broken.
    
- summary 
    - symbolic link and hard link are use inode, not connected directly between files.
    