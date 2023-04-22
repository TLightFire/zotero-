function sm(strs) {
    const smlist = 'bpmfdtnlgkhjqxrzcsyw';
    const nosm = ['eR', 'aN', 'eN', 'iN', 'uN', 'vN', 'nG', 'NG'];
    const rep = {'ZH':'Zh', 'CH':'Ch', 'SH':'Sh'};
    for (let i = 0; i < smlist.length; i++) {
        const s = smlist[i];
        strs = strs.replace(new RegExp(s, 'g'), s.toUpperCase());
    }
    for (let i = 0; i < nosm.length; i++) {
        const s = nosm[i];
        strs = strs.replace(new RegExp(s, 'g'), s.toLowerCase());
    }
    for (const key in rep) {
        strs = strs.replace(new RegExp(key, 'g'), rep[key]);
    }
    for (let i = 0; i < nosm.length; i++) {
        const s = nosm[i];
        let tmp_num = 0;
        let isOk = false;
        while (tmp_num > strs.length && !isOk) {
            try {
                tmp_num = strs.indexOf(s.toLowerCase(), tmp_num);
            } catch (err) {
                isOk = true;
                console.log(err);
            }
            if (!isOk) {
                tmp_num += s.length;
            }
            if (tmp_num < strs.length && !smlist.includes(strs[tmp_num].toLowerCase())) {
                strs = strs.substring(0, tmp_num - 1) + strs[tmp_num - 1].toUpperCase() + strs.substring(tmp_num);
            }
        }
    }
    return strs;
}

function onep(strs) {
    restr = sm(strs);
    return restr.replace(/[-‐\s]+/g, '').replace(/(?=[A-Z])/g, ' ');
}

let rn = 0;
let fewFieldMode = 0;
let lewFieldMode = 0;

await Zotero.DB.executeTransaction(async function () {
  const zoteroPane = Zotero.getActiveZoteroPane();
  const items = zoteroPane.getSelectedItems();
  const creatorsArray = items.map(item => item.getCreators());

  for (let creators of creatorsArray) {
    const newCreators = creators.map(creator => {
      let firstName = creator.firstName;
      let lastName = creator.lastName;
      
      const nFirstName = onep(firstName);
      const nLastName = onep(lastName);

      const firstNameSpaces = (nFirstName || "").split(" ").filter(s => Boolean(s)).length;
      const lastNameSpaces = (nLastName || "").split(" ").filter(s => Boolean(s)).length;
      const spaces = firstNameSpaces + lastNameSpaces;


      if (spaces > 3) {
          
        creator.firstName = firstName.replace(/[-‐\s]+/g, '').replace(/(?=[A-Z])/g, ' ');
        creator.lastName = lastName.replace(/[-‐\s]+/g, '').replace(/(?=[A-Z])/g, ' ');
        
        
      } else {
        creator.firstName = nFirstName;
        creator.lastName = nLastName;
      }

      if (/\s/g.test(nFirstName)) {
        fewFieldMode++;
      }
      if (/\s/g.test(nLastName)) {
        lewFieldMode++;
      }

      return creator;
    });

    items[creatorsArray.indexOf(creators)].setCreators(newCreators);
    rn++;
    await items[creatorsArray.indexOf(creators)].save();
  }

  if (fewFieldMode < lewFieldMode) {
    for (let creators of creatorsArray) {
      const newCreators = creators.map(creator => {
        const firstName = creator.firstName;
        const lastName = creator.lastName;
        creator.firstName = lastName;
        creator.lastName = firstName;
        return creator;
      });

      items[creatorsArray.indexOf(creators)].setCreators(newCreators);
      await items[creatorsArray.indexOf(creators)].save();
    }
  }
});

return rn + " item(s) updated";


